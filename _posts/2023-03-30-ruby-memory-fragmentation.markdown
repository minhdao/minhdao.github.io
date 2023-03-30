---
layout: post
title: "Ruby Memory management and fragmentation"
date: 2023-03-30
categories:
  - ruby
description: A look into how Ruby manages memory. What is memory fragmentation? What are the causes of fragmentation in Ruby?
---

A while ago I read a great post about memory fragmentation issues in multithreaded Ruby programs from Speed Shop. The post went through many different aspects of Ruby memory management and causes of fragmentation. It was a tough read, but also lots of fun. The full post can be found [here](https://www.speedshop.co/2017/12/04/malloc-doubles-ruby-memory.html)

In this note, I will mainly focus on memory management and fragmentation concepts to make it easier to review. I also put in some more illustration to some tougher concepts.

Ruby is not known to be memory friendly. A Ruby process can easily take up to 1 Gb.

A difference between memory leak and fragmentation that can be spotted is the rate of growth between the two. Memory leak often have a linear grow `O(N)` while fragmentation has a smooth logarithmic grow `O(log(N))`

Unfortunately, all Ruby programs suffer from some memory fragmentation. This is the result of how Ruby manages memory. The reason is Ruby cannot move objects in memory. If the objects are moved, then the raw pointers to these objects are no longer valid and can cause breaking in the program. If the objects cannot be moved, then it’s likely to leave “holes: in the memory, hence causing fragmentation.

## Ruby Memory Abstraction

Memory management is abstracted away from Ruby programmers through many layers:

![memory_layers_ruby](https://cdn.discordapp.com/attachments/1090947545477427264/1090947660975980615/image.png)

The problem of memory fragmentation is at the Ruby virtual machine Object Space and Allocator, to which many Ruby programmers are not familiar with.

Some level of fragmentation is inevitable and acceptable, but if it goes so that like taking up double or more memory than it should, then we should try to put in some solution to mitigate it.

### Ruby Object Space

Every Ruby object currently alive in a process has an entry in the Object Space.

You can think of Object Space as a big list of entries of Ruby Objects.

Each entry in the Object Space is `RVALUE` type which is a 40-byte C struct. Each entry contains data about the object. However, if the object exceeds the size of the entry, the object will be stored outside of the entry and the entry only contains a raw pointer to the object in the memory.

Entries are further organized in the different pages. Each page is about 16 KB and contains about 408 entries (`RVALUEs`)

The `RVALUE` can be confirmed by the properties below in a Ruby process:

```ruby
GC::INTERNAL_CONSTANTS
=> {
:RVALUE_SIZE=>40,
:HEAP_PAGE_OBJ_LIMIT=>408,
# ...
}
```

A Ruby program will create objects when needed, so the numbers of entries and the Object Space will grow. Therefore when:
1. Object Space runs out of space, a `malloc` command is issued to create a new page(s)
2. Object’s data for the entry exceeds its predefined size, a `malloc` command is issued to get memory space for the data and return a pointer to “keep in touch” with it

The `malloc` command issued in these cases does not provide an actual location or any indication of contiguity. It simply just asks for a space with a size and that space can be **anywhere** on the memory.

Ruby provides some memory fragmentation measures on its Object Space. `GC.stat` provides lots of information about the Object Space.

```ruby
GC.stat
=> {
:heap_eden_pages=>24508,
:heap_live_slots=>83,
# ... way more keys ...
}
```

`heap_live_slots` refers to the number of slots in the Object Space where entries of live objects still live.

`heap_eden_pages` refers to pages with at least one live slot. GC can NOT return Eden space back to the OS.

On the contrary, pages without any live slots are referred to as tomb pages. GC can return the tomb space back to the OS.

If we divide the number of live slots with the total number of all slots in eden pages, we will get a number representing how much space not occupied:
```ruby
heap_live_slots / (heap_eden_pages * slot_per_page) = 72.3%
``` 
This number means 72.3% of the slots in the eden pages are free, only 28% of the slots are occupied.

Another measure of fragmentation can be seen in the length of the  Object Space pages. For example, if we have 3 pages in total, but only page 1 and page 3 have live slots, and page 2 is a tomb page, the actual length of the pages is still 3. This is because the pages cannot be moved in the memory. We cannot shorten the page length by moving page 3 close to page 1 and replacing page 2.

Beside returning the tomb page space back to the OS, the GC can also prioritize putting the new objects in the eden pages. These measures help to reduce the fragmentation inside the Object Space

## The real cause of bad fragmentation

However, the Object Space fragmentation as mentioned above does not cause the bad levels of memory fragmentation. Most of the fragmentation happens outside of the Object Space when allocating memory for objects that do not fit inside a single Object Space entry (`RVALUE`).

The picture below shows that only around 15% of fragmentation is inside the Object Space, the rest is the result of calling `malloc` for objects that cannot be stored neatly inside a single entry. Orginal post can be found [here](https://twitter.com/tenderlove/status/879870368680255489?ref_src=twsrc%5Etfw%7Ctwcamp%5Etweetembed%7Ctwterm%5E879870368680255489%7Ctwgr%5E7d73701eb304c1e2c88a87005d6543c20edebc47%7Ctwcon%5Es1_&ref_url=https%3A%2F%2Fwww.speedshop.co%2F2017%2F12%2F04%2Fmalloc-doubles-ruby-memory.html).

![os_vs_malloc](https://cdn.discordapp.com/attachments/1090947545477427264/1090951650627305522/image.png)

## Summary

Memory fragmentation is an issue for the Ruby language. The cause of this issue is mainly because of the `malloc` calls outside the Object Space without any guarantee of contiguity. Furthermore, once created these memory areas are pretty much set in stone to maintain correct position in the memory so the pointers can be used correctly.

The picture below tries to capture the ideal of Ruby memory allocation inside and outside Object Space. We can see that the actual `malloc` calls to the allocator to create a memory space for the oversize objects are likely the cause of fragmentation.

![memory_management](https://cdn.discordapp.com/attachments/1090947545477427264/1090953839043149956/image.png)

Being aware of this knowledge, we can be more mindful and try to avoid fragmentation where possible in the code. It also helps to understand the more serious fragmentation issues in multithreaded Ruby programs.
