---
category: Language
tags: [Cpp]
usemath: [latex, ascii] 
---

# Helpful Algorithms

## Vector Algorithms

Like arrays, vectors can be used to search for particular element and to find a minimum or maximum element. Additionally, vectors can reverse the order of elements rather than just simply printing the elements in reverse order.



### Searching for a Particular Element

```c++
vector<string> cars(0);
string Camry = "A Camry is not available."; //default string value

cars.push_back("Corolla");
cars.push_back("Camry");
cars.push_back("Prius");
cars.push_back("RAV4");
cars.push_back("Highlander");

for (auto a : cars) { //enhanced for loop
  if (a == "Camry") { //if "Camry" is in vector
    Camry = "A Camry is available."; //variable changes if "Camry" exists
  }
}
    
cout << Camry << endl; //print whether Camry exists or not
```



### 2D Array with Enhanced For Loop

Like arrays and vectors, 2D arrays can also make use of the **enhanced** `for` loop.

```c++
int digits[3][3] = { {1, 2, 3},
                     {4, 5, 6}, 
                     {7, 8, 9} };

for (auto &i : digits) {
  for (int j : i) {
    if ((j == 3) | (j == 6) | (j == 9)) {
      cout << j << endl;
    }
    else {
      cout << j << " ";
    }
  }
}
```

