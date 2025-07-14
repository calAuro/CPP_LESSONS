# Schema Comparativo Base: C vs C++ vs C++ Moderno
## (Fondamenti Ritchie-Kernighan)

| **COSTRUTTO BASE** | **ESEMPIO C** | **ESEMPIO C++** | **C++ MODERNO** | **NOTE** |
|-------------------|---------------|-----------------|-----------------|----------|
| **Dichiarazione Variabili** | `int x; float y; char nome[50];` | `int x; float y; char nome[50];` | `int x; float y; string nome; auto z = 42;` | C++ identico + auto + string |
| **Inizializzazione** | `int x = 10; int arr[3] = {1,2,3};` | `int x = 10; int arr[3] = {1,2,3}; int y(10);` | `int x{10}; auto arr = {1,2,3}; vector<int> v{1,2,3};` | C++ aggiunge {} per sicurezza |
| **Costanti** | `#define PI 3.14` oppure `const int MAX = 100;` | `const int MAX = 100;` (preferito) | `constexpr int MAX = 100; const double PI = 3.14159;` | constexpr valutata a compilazione |
| **Tipi di Dato** | `int, float, double, char, unsigned int` | `int, float, double, char, bool` (aggiunge bool) | `int32_t, uint16_t, size_t, auto x = 42;` | Tipi con dimensioni garantite |
| **Input/Output** | `printf("Valore: %d", x); scanf("%d", &x);` | `cout << "Valore: " << x; cin >> x;` | `cout << "Valore: " << x << endl; cin >> x;` | Stream C++ type-safe |
| **Selezione if-else** | `if (x > 0) printf("Pos"); else printf("Neg");` | `if (x > 0) cout << "Pos"; else cout << "Neg";` | `if (auto result = calc(); result > 0) cout << "Pos";` | C++17 aggiunge init in if |
| **Switch** | `switch(x) { case 1: printf("Uno"); break; default: printf("Altro"); }` | `switch(x) { case 1: cout << "Uno"; break; default: cout << "Altro"; }` | `switch(auto val = getValue(); val) { case 1: cout << "Uno"; break; }` | C++17 init in switch |
| **Ciclo for** | `for(int i = 0; i < 10; i++) printf("%d ", i);` | `for(int i = 0; i < 10; i++) cout << i << " ";` | `for(auto n : nums) cout << n << " ";` (range-based) | Range-based for rivoluzionario |
| **Ciclo while** | `int i = 0; while(i < 10) { printf("%d ", i); i++; }` | `int i = 0; while(i < 10) { cout << i << " "; i++; }` | `int i = 0; while(i < 10) cout << i++ << " ";` | Identico al C |
| **Definizione Funzione** | `int somma(int a, int b) { return a + b; }` | `int somma(int a, int b) { return a + b; }` | `auto somma(int a, int b) -> int { return a + b; }` oppure template | Auto return type + template |
| **Prototipo Funzione** | `int somma(int a, int b);` (in header) | `int somma(int a, int b);` (in header) | `auto somma(int a, int b) -> int;` oppure `inline int veloce(int x) { return x*2; }` | Inline + template in header |
| **Parametri Default** | NON SUPPORTATO (devi fare overload manuale) | `int potenza(int base, int exp = 2) { ... }` | `int potenza(int base, int exp = 2) { return pow(base, exp); }` | Parametri default solo C++ |
| **Overload Funzioni** | NON SUPPORTATO (`somma_int`, `somma_float` separati) | `int somma(int a, int b); float somma(float a, float b);` | `template<typename T> T somma(T a, T b) { return a + b; }` | Template eliminano overload |
| **Scope Variabili** | Globale, locale, blocco con `{ }` | Globale, locale, blocco con `{ }` | Namespace scope + init in if: `if (auto z = getValue(); z > 0) { ... }` | Namespace + init in if/for |
| **Operatori** | `+, -, *, /, %, ==, !=, <, >, &&, ||, !` | Tutti quelli del C + `<<, >>` per I/O | Tutti + operator overloading: `Point p1 + Point p2;` | Operator overloading potente |
| **Commenti** | `/* multi-linea */` e `// singola` (C99) | `/* multi-linea */` e `// singola` | `// preferiti singola` + Doxygen `/** @brief ... */` | Doxygen per documentazione |
| **Casting** | `int x = (int)3.14; char* ptr = (char*)malloc(10);` | `int x = (int)3.14;` oppure `int y = int(3.14);` | `int x = static_cast<int>(3.14); char* ptr = reinterpret_cast<char*>(addr);` | Cast C++ type-safe |
| **Preprocessore** | `#include <stdio.h>` `#define MAX 100` `#ifdef DEBUG` | `#include <iostream>` `#define MAX 100` (sconsigliato) | `#include <iostream>` `const int MAX = 100;` `if constexpr (DEBUG) { ... }` | if constexpr sostituisce #ifdef |

## Esempi di Codice Completi

### Dichiarazione e Inizializzazione
```c
// C
int x;
float y = 3.14;
char nome[50];
strcpy(nome, "Mario");
```

```cpp
// C++ Base (identico)
int x;
float y = 3.14;
char nome[50];
strcpy(nome, "Mario");
```

```cpp
// C++ Moderno
auto x = 42;           // Tipo dedotto automaticamente
auto y = 3.14f;        // float
string nome = "Mario"; // Gestione automatica memoria
```

### Funzioni con Overload
```c
// C - Devi creare funzioni separate
int somma_int(int a, int b) { return a + b; }
float somma_float(float a, float b) { return a + b; }
double somma_double(double a, double b) { return a + b; }
```

```cpp
// C++ Base - Overload
int somma(int a, int b) { return a + b; }
float somma(float a, float b) { return a + b; }
double somma(double a, double b) { return a + b; }
```

```cpp
// C++ Moderno - Template (UNA funzione per tutti!)
template<typename T>
T somma(T a, T b) { 
    return a + b; 
}
// Uso: somma(5, 3), somma(2.5f, 1.5f), somma(1.0, 2.0)
```

### Cicli e Range-Based For
```c
// C
int numeri[] = {1, 2, 3, 4, 5};
int size = sizeof(numeri) / sizeof(numeri[0]);
for(int i = 0; i < size; i++) {
    printf("%d ", numeri[i]);
}
```

```cpp
// C++ Base (identico)
int numeri[] = {1, 2, 3, 4, 5};
int size = sizeof(numeri) / sizeof(numeri[0]);
for(int i = 0; i < size; i++) {
    cout << numeri[i] << " ";
}
```

```cpp
// C++ Moderno - Range-based for
vector<int> numeri = {1, 2, 3, 4, 5};
for(auto numero : numeri) {
    cout << numero << " ";
}
// Niente indici, niente size, niente errori!
```

## Progressione Didattica Consigliata

### 🟢 Settimane 1-2: Comfort Zone
"Tutto quello che sapete in C funziona identico in C++!"
- Stessi cicli, if-else, funzioni
- Solo `cout` invece di `printf`

### 🟡 Settimane 3-4: Potenziamenti Graduali  
"Stesso concetto, ma con opzioni in più"
- `bool` type, parametri default, overload
- `string` invece di `char[]`

### 🔵 Settimane 5+: Rivoluzione
"Guardate quanta roba in meno dovete scrivere!"
- `auto`, range-based for, template
- **Momento SHOCK garantito!** 🤯

## Il Momento "WHOA!" 💥

**Da 15 righe di C:**
```c
int somma_int(int a, int b) { return a + b; }
float somma_float(float a, float b) { return a + b; }
double somma_double(double a, double b) { return a + b; }

int numeri[] = {1,2,3,4,5};
for(int i = 0; i < 5; i++) {
    printf("%d ", numeri[i]);
}
```

**A 3 righe di C++ moderno:**
```cpp
template<typename T> T somma(T a, T b) { return a + b; }
vector<int> numeri = {1,2,3,4,5};
for(auto n : numeri) cout << n << " ";
```

**I tuoi studenti:** "Ma questo è illegale!" 😱🚀