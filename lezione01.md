# Lezione 1: Fondamenti C++

## Obiettivo
Primi passi dal C al C++: input/output, stringhe e array statici.

## Differenze principali C vs C++

### 1. Hello World
```cpp
// C
#include <stdio.h>
int main() {
    printf("Ciao mondo\n");
    return 0;
}

// C++
#include <iostream>
using namespace std;
int main() {
    cout << "Ciao mondo" << endl;
    return 0;
}
```

### 2. Input/Output
```cpp
// Input semplice (si ferma al primo spazio)
string nome;
cin >> nome;

// Input completo (intera riga)
string nome_completo;
getline(cin, nome_completo);
```

### 3. Stringhe
```cpp
// In C++: gestione automatica della memoria
string nome = "Mario";
cout << "Lunghezza: " << nome.length() << endl;

// Controlli utili
if (nome.empty()) {
    cout << "Stringa vuota!" << endl;
}
```

## Array Statici in C++

### 1. Dichiarazione (come in C)
```cpp
int numeri[5] = {10, 20, 30, 40, 50};
int altri[] = {1, 2, 3};  // dimensione automatica
int zeri[5] {};           // tutti zero
```

### 2. Array moderni (std::array)
```cpp
#include <array>
array<int, 5> numeri = {10, 20, 30, 40, 50};
cout << "Dimensione: " << numeri.size() << endl;
```

### 3. Range-based for (stile Python)
```cpp
int numeri[] = {10, 20, 30};

// Modo tradizionale
for (int i = 0; i < 3; i++) {
    cout << numeri[i] << " ";
}

// Modo moderno
for (int n : numeri) {
    cout << n << " ";
}

// Con auto (tipo automatico)
for (auto n : numeri) {
    cout << n << " ";
}
```

## Codice Completo di Prova
```cpp
#include <iostream>
#include <array>
using namespace std;

int main() {
    // Test stringhe
    string nome;
    cout << "Nome completo: ";
    getline(cin, nome);
    
    if (nome.empty()) {
        cout << "Nessun nome inserito!" << endl;
    } else {
        cout << "Ciao " << nome << "!" << endl;
        cout << "Il tuo nome e' lungo " << nome.length() << " caratteri" << endl;
    }
    
    // Test array
    int numeri[] = {10, 20, 30};
    array<int, 5> num = {15, 20, 25, 30, 35};
    
    cout << "\nArray tradizionale: ";
    for (int n : numeri) {
        cout << n << " - ";
    }
    
    cout << "\nArray moderno: ";
    for (int n : num) {
        cout << n << " * ";
    }
    cout << endl;
    
    return 0;
}
```

## Compilazione
```bash
g++ -o programma programma.cpp
./programma
```

## Vantaggi rispetto al C
- **Stringhe**: gestione automatica della memoria
- **Input/Output**: più sicuro di printf/scanf
- **Array**: metodi integrati (.size(), .empty(), ecc.)
- **Sintassi**: più leggibile (range-based for)

## Prossima Lezione
Array dinamici con `std::vector` - addio malloc/free!

---
*Salvato il: 05-07-2025*
