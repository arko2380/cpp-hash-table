# cpp-hash-table
C++ Hash Table

#include <cstdlib>
#include <cassert>
#include <string>
#include <iostream>
using namespace std;

template <class RecordType>
class table
{ public:
     static const std::size_t CAPACITY = 811;
     table();
     void insert(const RecordType& entry);
     void remove(int key);
     bool is_present(int key) const;
     void find(int key, bool& found, RecordType& result) const;
     std::size_t size() const
     { return used; }

  private:
      static const int NEVER_USED = -1;
      static const int PREVIOUSLY_USED = -2;
      RecordType data[CAPACITY];
      std::size_t used;
      std::size_t hash(int key) const;
      std::size_t next_index(std::size_t index) const;
      void find_index(int key, bool& found, std::size_t& index) const;
      bool never_used(std::size_t index) const;
      bool is_vacant(std::size_t index) const;
};

template <class RecordType>
const std::size_t table<RecordType>::CAPACITY;

template <class RecordType>
const int table<RecordType>::NEVER_USED;

template <class RecordType>
const int table<RecordType>::PREVIOUSLY_USED;

template <class RecordType>
table<RecordType>::table()
{ std::size_t i;

  used = 0;
  for(i = 0; i < CAPACITY; ++i)
    data[i].key = NEVER_USED;
}

//Code that inserts one item anywhere in the table
template class<RecordType>
void table<RecordType>::insert(const RecordType& entry)
{ bool already_present;
  std::size_t index;
  std::size_t count = 0;
  assert(entry.key >= 0);
  find_index(entry.key, already_present, index);
  if(!already_present)
    { assert(size() < CAPACITY);
      index = hash(entry.key);
        while(!is_vacant(index))
          { //index = next_index(index);
            index = (index + count) % CAPACITY;
            count++;
           }
      ++used;
     }
  data[index] = entry;
}

//Code that removes one item from anywhere in the table
template <class RecordType>
void table<RecordType>::remove(int key)
{ bool found;
  std::size_t index;
  assert(key >= 0);
  find_index(key, found, index);
  if(found)
    { data[index].key = PREVIOUSLY_USED;
      --used;
    }
}

//Code that checks whether an item exists in the table
template <class RecordType>
bool table<RecordType>::is_present(int key) const
{ bool found;
  std::size_t index;
  assert(key >= 0);
  find_index(key, found, index);
  return found;
}

//Code that finds one item in the table
template <class RecordType>
void table<RecordType>::find(int key, bool& found, RecordType& result) const
{ std::size_t index;
  assert(key >= 0);
  find_index(key, found, index);
  if(found)
    result = data[index];
}

//Code that hashes one item
template <class RecordType>
inline std::size_t table<RecordType>::hash(int key) const
{ return(key % CAPACITY);
}

//Code that moves the cursor around in the table
template <class RecordType>
inline std::size_t table<RecordType>::next_index(std::size_t index) const
{ return((index + 1) % CAPACITY);
}

//Code that finds one indexed item
template <class RecordType>
void table<RecordType>::find_index(int key, bool& found, std::size_t& i) const
{ std::size_t count;
  count = 0;
  i = hash(key);
  while((count < CAPACITY) && (data[i].key != NEVER_USED) && (data[i].key != key))
    { ++count:
       // i = next_index(i);
      i = (i + count) % CAPACITY;
     }
  found = (data[i].key == key);
}

//Code that checks if an item has been used in a search
template <class RecordType>
inline bool table<RecordType>::never_used(std::size_t index) const
{ return(data[index].key == NEVER_USED); }

//Code that checks for empty indexes in the table
template <class RecordType>
inline bool table<RecordType>::is_vacant(std::size_t index) const
{ return(data[index].key == NEVER_USED || (data[index].key == PREVIOUSLY_USED); }

//Code that stores an item's data
struct data_record
{ int key;
  string name;
};


//Main Test
typedef struct data_record Data;
int main()
{ Data d = { 1, "Chris" };
  Data d2 = { 2, "Chris2" };
  Data d3 = { 3, "Chris3" };
  Data d4 = { 4, "Chris4" };
  table<data_record> my_table;
  my_table.insert(d3);
  my_table.insert(d4);
  Data d_out;
  bool found;
  my_table.find(3, found, d_out);
  cout << d_out.name << endl;
  my_table.find(3, found, d_out);
  cout << d_out.name << endl;

  return 0;
}
