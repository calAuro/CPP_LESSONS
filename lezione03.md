# Lezione 3: STL Containers

## Obiettivo
Imparare le strutture dati pronte all'uso di C++: stack, queue e map.

## Dal C al C++: Strutture Dati

### Problema in C
```c
// In C dovevi implementare tutto manualmente:
struct Node {
    int data;
    struct Node* next;
};

// Gestione pila con malloc/free
struct Node* push(struct Node* top, int value) {
    struct Node* new_node = malloc(sizeof(struct Node));
    new_node->data = value;
    new_node->next = top;
    return new_node;
}
```

### Soluzione C++
```cpp
#include <stack>
stack<int> pila;
pila.push(value);  // Fatto! Tutto automatico
```

## 1. STACK (Pila) - LIFO

### Concetto
Last In, First Out - ultimo inserito, primo estratto.

### Operazioni Base
```cpp
#include <iostream>
#include <stack>
using namespace std;

int main() {
    stack<int> pila;
    
    // Inserimento
    pila.push(10);
    pila.push(20);
    pila.push(30);
    
    // Lettura e controlli
    cout << "Dimensione: " << pila.size() << endl;
    cout << "Elemento in cima: " << pila.top() << endl;
    cout << "E' vuota? " << (pila.empty() ? "Si" : "No") << endl;
    
    return 0;
}
```

### Svuotamento Stack
```cpp
int elemento;

cout << "=== SVUOTO LA PILA ===" << endl;
while (!pila.empty()) {
    elemento = pila.top();  // Leggi elemento in cima
    cout << "Tolgo: " << elemento << endl;
    pila.pop();            // Rimuovi elemento
}
```

**Output:** 30, 20, 10 (ordine inverso di inserimento)

## 2. QUEUE (Coda) - FIFO

### Concetto
First In, First Out - primo inserito, primo estratto.

### Operazioni Base
```cpp
#include <iostream>
#include <queue>
using namespace std;

int main() {
    queue<int> coda;
    
    // Inserimento
    coda.push(10);
    coda.push(20);
    coda.push(30);
    
    // Lettura
    cout << "Primo della coda: " << coda.front() << endl;
    cout << "Ultimo della coda: " << coda.back() << endl;
    cout << "Dimensione: " << coda.size() << endl;
    
    return 0;
}
```

### Svuotamento Queue
```cpp
int elemento;

cout << "=== SVUOTO LA CODA ===" << endl;
while (!coda.empty()) {
    elemento = coda.front();  // Leggi primo elemento
    cout << "Tolgo: " << elemento << endl;
    coda.pop();              // Rimuovi primo elemento
}
```

**Output:** 10, 20, 30 (stesso ordine di inserimento)

## 3. MAP (Mappa) - Chiave-Valore

### Concetto
Associa una chiave univoca a un valore. Come una rubrica telefonica.

### Operazioni Base
```cpp
#include <iostream>
#include <map>
#include <string>
using namespace std;

int main() {
    map<string, int> voti;
    
    // Inserimento
    voti["Mario"] = 28;
    voti["Luigi"] = 25;
    voti["Peach"] = 30;
    
    // Lettura
    cout << "Voto di Mario: " << voti["Mario"] << endl;
    cout << "Numero studenti: " << voti.size() << endl;
    
    return 0;
}
```

### Iterare su una Map
```cpp
cout << "=== TUTTI I VOTI ===" << endl;
for (auto coppia : voti) {
    cout << "Studente: " << coppia.first;      // chiave
    cout << " - Voto: " << coppia.second << endl;  // valore
}
```

**Nota:** La map ordina automaticamente le chiavi alfabeticamente!

## Confronto con Implementazione C

### Map in C (con malloc)
```c
struct Studente {
    char* nome;
    int voto;
};

struct Database {
    struct Studente* studenti;
    int size;
    int capacity;
};

// + funzioni per: crea_database(), trova_studente(), 
// imposta_voto(), ridimensiona(), distruggi_database()
// Circa 80 righe di codice!
```

### Map in C++
```cpp
map<string, int> voti;
voti["Mario"] = 28;           // 2 righe!
cout << voti["Mario"];
```

## Esempio Pratico Combinato

### Sistema Gestione Coda Ufficio
```cpp
#include <iostream>
#include <queue>
#include <map>
#include <string>
using namespace std;

int main() {
    queue<string> coda_clienti;
    map<string, string> servizi;
    string cliente;
    
    // Setup
    coda_clienti.push("Mario");
    coda_clienti.push("Luigi");
    coda_clienti.push("Peach");
    
    servizi["Mario"] = "Passaporto";
    servizi["Luigi"] = "Carta identita";
    servizi["Peach"] = "Patente";
    
    // Gestione coda
    cout << "=== GESTIONE CODA UFFICIO ===" << endl;
    while (!coda_clienti.empty()) {
        cliente = coda_clienti.front();
        cout << "Servo: " << cliente;
        cout << " - Servizio: " << servizi[cliente] << endl;
        coda_clienti.pop();
    }
    
    return 0;
}
```

## Riepilogo Utilizzi

| Container | Quando Usarlo | Esempio Applicativo |
|-----------|---------------|-------------------|
| **stack** | LIFO, Undo/Redo | Storico azioni, chiamate funzioni |
| **queue** | FIFO, Code di attesa | Processi, richieste in ordine |
| **map** | Ricerca per chiave | Rubrica, catalogo, database |

## Metodi Principali

### Stack
- `push(value)` - inserisce in cima
- `pop()` - rimuove dalla cima
- `top()` - legge elemento in cima
- `empty()` - true se vuota
- `size()` - numero elementi

### Queue  
- `push(value)` - inserisce in fondo
- `pop()` - rimuove dal fronte
- `front()` - legge primo elemento
- `back()` - legge ultimo elemento
- `empty()` - true se vuota
- `size()` - numero elementi

### Map
- `mappa[chiave] = valore` - inserisce/aggiorna
- `mappa[chiave]` - legge valore
- `mappa.size()` - numero elementi
- `mappa.empty()` - true se vuota

## Vantaggi vs C
- ✅ **Niente malloc/free**: Gestione automatica memoria
- ✅ **Sicurezza**: Controlli automatici sui limiti
- ✅ **Semplicità**: Operazioni complesse in una riga
- ✅ **Ottimizzazione**: Implementazioni efficienti pre-testate
- ✅ **Leggibilità**: Codice più espressivo e manutenibile

## Prossima Lezione
Algoritmi STL: sort, find, transform - addio ai loop manuali!

---
*Salvato il: [data corrente]*
