# Lezione 2: Array Dinamici con Vector

## Obiettivo
Sostituire malloc/free con vector - array dinamici sicuri e automatici.

## Problema del C
```c
// C: gestione manuale della memoria
int* array = malloc(10 * sizeof(int));
// ... uso l'array
free(array);  // OBBLIGATORIO! Sennò memory leak
```

## Soluzione C++
```cpp
#include <vector>
vector<int> array(10);  // Fatto! Gestione automatica
// Quando finisce il programma, si pulisce da solo
```

## Operazioni Base

### 1. Creazione Vector
```cpp
vector<int> vuoto;                          // vuoto (size = 0)
vector<int> con_dimensione(5);              // 5 elementi = 0
vector<int> con_valori = {10, 20, 30, 40};  // con valori specifici
```

### 2. Aggiungere Elementi
```cpp
vector<int> numeri;
numeri.push_back(10);   // aggiunge 10
numeri.push_back(20);   // aggiunge 20
numeri.push_back(30);   // aggiunge 30
// Ora: size() = 3
```

### 3. Accesso agli Elementi
```cpp
cout << numeri[0];        // primo elemento (come array C)
cout << numeri.front();   // primo elemento (sicuro)
cout << numeri.back();    // ultimo elemento (sicuro)
cout << numeri.size();    // numero elementi
```

### 4. Rimuovere Elementi
```cpp
numeri.pop_back();        // rimuove ultimo elemento
```

### 5. Controlli Sicurezza
```cpp
if (numeri.empty()) {
    cout << "Vector vuoto!" << endl;
} else {
    cout << "Ultimo: " << numeri.back() << endl;
}
```

## Range-based For
```cpp
vector<int> numeri = {10, 20, 30};

// Modo tradizionale
for (int i = 0; i < numeri.size(); i++) {
    cout << numeri[i] << " ";
}

// Modo moderno (come Python)
for (int n : numeri) {     // "per ogni valore nel vector"
    cout << n << " ";      // n prende i valori 10, 20, 30
}

// Con auto (tipo automatico)
for (auto n : numeri) {
    cout << n << " ";
}
```

## La Parola Chiave 'auto'
```cpp
// Invece di specificare il tipo:
int numero = 42;
string nome = "Mario";

// Il compilatore lo deduce:
auto numero = 42;        // diventa int
auto nome = "Mario";     // diventa string

// Utile nei for:
for (auto n : numeri) {  // auto = int (tipo degli elementi)
    cout << n;
}
```

## Codice Completo di Esempio
```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> numeri = {10, 20, 30};
    
    cout << "Dimensione iniziale: " << numeri.size() << endl;
    
    // Aggiungiamo elementi
    numeri.push_back(40);
    numeri.push_back(50);
    
    cout << "Tutti i numeri: ";
    for (auto n : numeri) {
        cout << n << " ";
    }
    cout << endl;
    
    // Rimuoviamo elementi
    while (!numeri.empty()) {
        cout << "Rimuovo: " << numeri.back() << endl;
        numeri.pop_back();
    }
    
    cout << "Vector vuoto! Size = " << numeri.size() << endl;
    
    return 0;
}
```

## Vantaggi Vector vs Array C
- ✅ **Sicurezza**: Niente memory leak
- ✅ **Semplicità**: Niente malloc/free
- ✅ **Dinamico**: Cresce/decresce automaticamente
- ✅ **Metodi utili**: size(), empty(), front(), back()
- ✅ **Compatibilità**: Accesso con [] come array C

## Prossima Lezione
STL Containers: stack, queue, map - strutture dati pronte all'uso!

---
*Salvato il: [data corrente]*
