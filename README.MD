## Introduction

In this lab, you are going to create a simple linked list with an
iterator. The first part of the lab will be for you to type the list.h
and list.cpp code. For maximum benefit, you should type the code, and
not copy and paste it\! Once you have that done, you will be writing a
main.cpp file which will make the list perform its various actions.

We will end the lab by doing a test to see if there are any memory leaks
in your code.

So let's get started\!

## Makefile

The first order of business is to create our Makefile. Remember that you
will have to turn off tab expansion if you are using the SMC editor.

``` make
CXXFLAGS=--std=gnu++11 -g
CC=g++
LD=g++
TARGETS=listTest
 
listTest: list.o main.o
list.o: list.cpp list.h
 
clean:
        rm -f *.o a.out $(TARGETS)
```

## list.h

Here is the code for the list.h file. Type it into a file called
"list.h".

``` cpp
// File: list.h
// Purpose: This is a simple linked list with iterator.
#ifndef LIST_H
#define LIST_H

class List
{
private:
    //the class node for the list
    class Node 
    {
    public:
        int data;
        Node *next;
    };
   
    //the beginning of the list
    Node *root;
    
public:
    List();   //constructor
    ~List();  //destructor
    
    //iterator inner class
    class iterator
    {
    public:
        iterator();
        iterator(const iterator &other);
        int & operator*();  //dereference operator
        iterator operator++(); //prefix increment
        iterator operator++(int); //postfix increment
        bool operator==(const iterator &other);
        bool operator!=(const iterator &other);
        iterator & operator=(const iterator &other);
    private:
        Node* cur;
        friend List;
    };
   
    //iterator operations
    iterator begin();
    iterator end();
    
    //list operations
    void append(int item);  //appends the item to the end of the list
    void prepend(int item); //inserts an item at the root of the list
    void insert(const iterator &itr, int item);  //inserts an item after the iteartor itr
    void remove(iterator &itr);  //removes the item pointed to by iterator from the list and invalidates iterator 
};

#endif
```

## list.cpp

Here is the code for list.cpp, go ahead and type it in.

``` cpp
#include "list.h"

List::List()   //constructor
{
    root = nullptr;
}

List::~List()  //destructor
{
    Node *cur;
    while(root) {
        //get the current node and advance the root
        cur = root;
        root = root->next;
        
        //mwahaha
        delete cur;
    }
}


//iterator constructor
List::iterator::iterator() 
{
    cur = nullptr;
}


//iterator copy constructor
List::iterator::iterator(const iterator &other)
{
    cur = other.cur;
}


int &
List::iterator::operator*()  //dereference operator
{
    return cur->data;
}


List::iterator
List::iterator::operator++() //prefix increment
{
    cur = cur->next;
    return *this;
}


List::iterator 
List::iterator::operator++(int) //postfix increment
{
    //get the current value into a new iterator
    List::iterator itr = *this;
    ++(*this); //do the increment
   
    //return the old position
    return itr;
}


bool 
List::iterator::operator==(const iterator &other)
{
    return cur == other.cur;
}


bool 
List::iterator::operator!=(const iterator &other)
{
    return cur != other.cur;
}


List::iterator & 
List::iterator::operator=(const iterator &other)
{
    //copy the pointer
    cur = other.cur;
    
    return *this;
}
   
//iterator operations
List::iterator 
List::begin()
{
    //create an iterator and point it at root
    List::iterator itr;
    itr.cur = root;
    return itr;
}


List::iterator 
List::end()
{
    //create an iterator with a null pointer
    List::iterator itr;
    itr.cur = nullptr;
    return itr;
}
    

//list operations
void 
List::append(int item)  //appends the item to the end of the list
{
    //build the node
    Node *node = new Node;
    node->data = item;
    node->next = nullptr;
    
    //empty lists are special cases!
    if(!root) {
        root = node;
        return;
    }
    
    //find the end
    Node *cur=root;
    while(cur->next) {
        cur = cur->next;
    }
        
    
    //insert the node
    cur->next = node;
}


void 
List::prepend(int item) //inserts an item at the root of the list
{
    //build the node
    Node *node = new Node;
    node->data = item;
    
    //link the node
    node->next = root;
    root = node;
}


void 
List::insert(const iterator &itr, int item)  //inserts an item after the iteartor itr
{
    //build the node
    Node *node = new Node;
    node->data = item;
    
    //link the node
    node->next = itr.cur->next;
    itr.cur->next = node;
}


void 
List::remove(iterator &itr)  //removes the item pointed to by iterator from the list and invalidates iterator 
{
    //get the doomed node and invalidate the iterator
    Node *doomed = itr.cur;
    itr.cur = nullptr;
    
    //handle the one special case
    if(root == doomed) {
        root = root->next;
        delete doomed;
        return;
    }
    
    //find the previous node
    Node *prev = root;
    while(prev->next != doomed) {
        prev = prev->next;
    }
    
    //unlink the doomed node
    prev->next = doomed->next;
    
    //FINISH HIM!
    delete doomed;
}
```

## Study the Files

Now, study the files you have just typed in. Try to form a mental
picture of how this class operates.

You'll also want to do a test compilation:

` make list.o`

Once you have no compiler errors, move on to the next step.

## main.cpp

Now the time has come to write main.cpp. Here is what you need to do:

  - Add an overloaded insertion operator which will print out the list.
    HINT: The prototype of this operator is as follows:

` ostream & operator<<(ostream &os, List &l);`

  - Create a list object. Remember, we use pointers to reference
    classes\!
  - Append the numbers 1 through 10 to the list and then print out the
    lsit
  - Add the number 0 to the head of the list and then print out the
    list.
  - Inert -7 after the third element in the list and then print out the
    list.
  - Change the value of the third element in the list to -6 and then
    print out the list.
  - Remove the head item from the list and then print out the list.
  - Remove the second item from the list and then print out the list.

The expected output of the program is shown below:

` After numbers 1-10: 1->2->3->4->5->6->7->8->9->10->/`  
` After prepend: 0->1->2->3->4->5->6->7->8->9->10->/`  
` After Insert: 0->1->2->-7->3->4->5->6->7->8->9->10->/`  
` After Iterator Change: 0->1->-6->-7->3->4->5->6->7->8->9->10->/`  
` After removing head: 1->-6->-7->3->4->5->6->7->8->9->10->/`  
` After removing second item: 1->-7->3->4->5->6->7->8->9->10->/`

Make sure your output matches this output before proceeding\!

## Testing With Valgrind

We have a lot of dynamic memory going on in this program\! That means we
have a strong potential for memory leaks to form. Valgrind is a tool
which will help evaluate memory allocation and deallocation. You will be
using this more in your data structures class, but let's take a little
peek at it now. Run the following in a terminal:

` valgrind ./listTest`

You will get a report about memory usage. If your program has no memory
leaks, you'll get something like
this:

` ==14229== Memcheck, a memory error detector`  
` ==14229== Copyright (C) 2002-2015, and GNU GPL'd, by Julian Seward et al.`  
` ==14229== Using Valgrind-3.11.0 and LibVEX; rerun with -h for copyright info`  
` ==14229== Command: ./listTest`  
` ==14229==`  
` After numbers 1-10: 1->2->3->4->5->6->7->8->9->10->/`  
` After prepend: 0->1->2->3->4->5->6->7->8->9->10->/`  
` After Insert: 0->1->2->-7->3->4->5->6->7->8->9->10->/`  
` After Iterator Change: 0->1->-6->-7->3->4->5->6->7->8->9->10->/`  
` After removing head: 1->-6->-7->3->4->5->6->7->8->9->10->/`  
` After removing second item: 1->-7->3->4->5->6->7->8->9->10->/`  
` ==14229==`  
` ==14229== HEAP SUMMARY:`  
` ==14229==     in use at exit: 72,704 bytes in 1 blocks`  
` ==14229==   total heap usage: 14 allocs, 13 frees, 72,904 bytes allocated`  
` ==14229==`  
` ==14229== LEAK SUMMARY:`  
` ==14229==    definitely lost: 0 bytes in 0 blocks`  
` ==14229==    indirectly lost: 0 bytes in 0 blocks`  
` ==14229==      possibly lost: 0 bytes in 0 blocks`  
` ==14229==    still reachable: 72,704 bytes in 1 blocks`  
` ==14229==         suppressed: 0 bytes in 0 blocks`  
` ==14229== Rerun with --leak-check=full to see details of leaked memory`  
` ==14229==`  
` ==14229== For counts of detected and suppressed errors, rerun with: -v`  
` ==14229== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)`

If the leak summary reports something other than 0 bytes in losses, then
you have a bug to fix\! I know that list.cpp's code should be leak free,
provided you copied it correctly. Is there anything that main.cpp
allocates that you haven't called delete on? Try to find it , fix it,
recompile, and run valgrind again. Keep going until you get valgrind to
report "0 bytes" for all the lost memory segments.
