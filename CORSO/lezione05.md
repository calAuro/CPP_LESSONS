# Lezione 5: Puntatori e Riferimenti

## Obiettivo
Capire i riferimenti C++ e risolvere il mistero `const Taxi&` della lezione 4.

## Ripasso: Puntatori in C++

### Puntatori C++ = Identici al C
```cpp
#include <iostream>
using namespace std;

int main() {
    int x = 42;
    int* ptr = &x;  // ptr punta all'indirizzo di x
    
    cout << "Valore di x: " << x << endl;           // 42
    cout << "Indirizzo di x: " << &x << endl;       // es: 0x61fe44
    cout << "Valore di ptr: " << ptr << endl;       // es: 0x61fe44 (stesso)
    cout << "Contenuto puntato: " << *ptr << endl;  // 42
    
    return 0;
}
```

**Niente di nuovo - tutto identico al C!**

## I Riferimenti: La Rivoluzione

### Confronto Diretto: Puntatori vs Riferimenti
```cpp
#include <iostream>
using namespace std;

int main() {
    int x = 42;
    
    // PUNTATORE (modo C/C++ tradizionale)
    int* ptr = &x;
    cout << "Con puntatore: " << *ptr << endl;  // Serve asterisco
    
    // RIFERIMENTO (modo C++ moderno)
    int& ref = x;  // ref È x, stesso posto in memoria
    cout << "Con riferimento: " << ref << endl;  // Niente asterisco!
    
    // Modifica tramite riferimento
    ref = 100;
    cout << "x ora vale: " << x << endl;  // x è cambiato a 100!
    
    return 0;
}
```

**Output:**
```
Con puntatore: 42
Con riferimento: 42
x ora vale: 100
```

### Cosa Sono i Riferimenti
- **Riferimento = Alias** di una variabile esistente
- **Stesso indirizzo** della variabile originale
- **Sintassi pulita** - niente `*` per dereferenziare
- **Inizializzazione obbligatoria** - devono puntare a qualcosa
- **Non riassegnabili** - sempre lo stesso oggetto

## Riferimenti nelle Funzioni

### Il Problema dei Puntatori
```cpp
#include <iostream>
using namespace std;

// METODO VECCHIO (puntatori)
void raddoppia_con_puntatore(int* numero) {
    *numero = *numero * 2;  // Asterischi ovunque!
}

// METODO NUOVO (riferimenti)  
void raddoppia_con_riferimento(int& numero) {
    numero = numero * 2;    // Sintassi naturale!
}

int main() {
    int x = 5;
    
    // Con puntatori
    raddoppia_con_puntatore(&x);  // Devi ricordare la &
    cout << "Dopo puntatore: " << x << endl;  // 10
    
    x = 5;  // Reset
    
    // Con riferimenti
    raddoppia_con_riferimento(x); // Chiamata normale!
    cout << "Dopo riferimento: " << x << endl;  // 10
    
    return 0;
}
```

**Output:**
```
Dopo puntatore: 10
Dopo riferimento: 10
```

### Vantaggi dei Riferimenti
| Aspetto | Puntatori | Riferimenti |
|---------|-----------|-------------|
| **Sintassi** | `*ptr`, `&var` | `ref` (naturale) |
| **Chiamata funzione** | `func(&x)` | `func(x)` |
| **Dereferenziazione** | `*ptr` | `ref` (automatica) |
| **Riassegnazione** | `ptr = &y` ✅ | Non possibile |
| **Null/Uninitialized** | Possibile 💀 | Impossibile ✅ |

## const e Riferimenti: Efficienza e Sicurezza

### Il Problema delle Copie
```cpp
#include <iostream>
#include <string>
using namespace std;

// LENTO: copia tutta la stringa
void stampa_lento(string nome) {
    cout << "Ciao " << nome << endl;
}

// VELOCE: riferimento (no copia)
void stampa_veloce(const string& nome) {
    cout << "Ciao " << nome << endl;
}

int main() {
    string nome_lungo = "Alessandro Manzoni Promessi Sposi";
    
    stampa_lento(nome_lungo);   // Copia 33 caratteri!
    stampa_veloce(nome_lungo);  // Zero copie!
    
    return 0;
}
```

### const& - La Combinazione Perfetta
```cpp
void elabora_dati(const vector<int>& dati) {
    // const& = efficiente + sicuro
    // - Niente copia (veloce)
    // - Non può modificare (sicuro)
    
    for (auto valore : dati) {
        cout << valore << " ";
    }
}
```

**`const string& nome` significa:**
- **`string&`** = riferimento (niente copia, veloce)
- **`const`** = non modificabile (sicuro)

## MISTERO RISOLTO: const Taxi&

### Ricordi questo dalla Lezione 4?
```cpp
[](const Taxi& a, const Taxi& b) {  // ECCO SPIEGATO!
    return a.distanza < b.distanza;
}
```

**Ora sai cosa significa:**
- **`Taxi& a`** = riferimento alla struct Taxi (niente copia pesante!)
- **`const`** = non modificabile (solo lettura per confronto)
- **Efficienza** = zero copie di struct, solo "alias"

### Confronto Efficienza
```cpp
struct Taxi {
    string id;
    string stato;
    double distanza;
    // ... altri campi
};

// LENTO: copia tutta la struct (3+ campi)
bool confronta_lento(Taxi a, Taxi b) {
    return a.distanza < b.distanza;
}

// VELOCE: zero copie
bool confronta_veloce(const Taxi& a, const Taxi& b) {
    return a.distanza < b.distanza;
}
```

**Per struct grandi, `const&` è ESSENZIALE!**

## Regole Pratiche per i Riferimenti

### Quando Usare Cosa

| Situazione | Usa | Esempio |
|------------|-----|---------|
| **Parametro input (small)** | Copia | `void func(int x)` |
| **Parametro input (large)** | `const&` | `void func(const string& s)` |
| **Parametro output** | `&` | `void func(int& result)` |
| **Parametro input/output** | `&` | `void func(vector<int>& data)` |
| **Variabile locale** | Normale | `int x = 42;` |
| **Alias locale** | `&` | `int& ref = x;` |

### Tipi "Small" vs "Large"
- **Small**: `int`, `float`, `double`, `char`, `bool` → copia
- **Large**: `string`, `vector`, `struct`, `class` → `const&`

## Esempi Pratici

### Passaggio Parametri Ottimale
```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

// Input piccolo: copia
int somma(int a, int b) {
    return a + b;
}

// Input grande: const&
void stampa_lista(const vector<int>& numeri) {
    for (auto n : numeri) {
        cout << n << " ";
    }
    cout << endl;
}

// Output: riferimento
void leggi_nome(string& nome) {
    cout << "Nome: ";
    getline(cin, nome);
}

// Input/Output: riferimento
void raddoppia_tutti(vector<int>& numeri) {
    for (auto& n : numeri) {  // Nota: auto& per modificare
        n *= 2;
    }
}

int main() {
    vector<int> dati = {1, 2, 3, 4, 5};
    string nome;
    
    cout << "Somma: " << somma(3, 4) << endl;
    
    cout << "Lista originale: ";
    stampa_lista(dati);
    
    raddoppia_tutti(dati);
    cout << "Lista raddoppiata: ";
    stampa_lista(dati);
    
    leggi_nome(nome);
    cout << "Ciao " << nome << "!" << endl;
    
    return 0;
}
```

### Riferimenti in Range-Based For
```cpp
vector<string> nomi = {"Mario", "Luigi", "Peach"};

// Solo lettura: auto (copia)
for (auto nome : nomi) {
    cout << nome << " ";  // nome è una copia
}

// Solo lettura efficiente: const auto&
for (const auto& nome : nomi) {
    cout << nome << " ";  // nome è riferimento const
}

// Modifica: auto&
for (auto& nome : nomi) {
    nome += "!";  // Modifica l'originale
}
```

## Errori Comuni con i Riferimenti

### ❌ Riferimento Non Inizializzato
```cpp
int& ref;  // ERRORE: riferimento deve essere inizializzato
```

### ❌ Riferimento a Temporaneo (Pericoloso)
```cpp
int& ref = 42;  // ERRORE: 42 è temporaneo
```

### ❌ Riassegnazione Riferimento
```cpp
int x = 10, y = 20;
int& ref = x;
ref = y;  // Non riassegna ref, ma modifica x = 20!
```

### ✅ Uso Corretto
```cpp
int x = 10;
int& ref = x;  // OK: riferimento a variabile esistente
ref = 20;      // OK: modifica x tramite ref
```

## Puntatori vs Riferimenti: Quando Usare Cosa

### Usa Puntatori Quando:
- Puoi avere valori NULL
- Devi riassegnare a oggetti diversi
- Aritmetica dei puntatori
- Compatibilità con C
- Array dinamici (ma meglio `vector`)

### Usa Riferimenti Quando:
- Parametri di funzione (input/output)
- Alias locali
- Sintassi più pulita
- Sicurezza (non NULL)
- Range-based for con modifica

## Il Momento "AHA!" 💡

**Prima (con puntatori):**
```cpp
void ordina_taxi(vector<Taxi>* flotta) {
    sort(flotta->begin(), flotta->end(),
         [](const Taxi* a, const Taxi* b) {  // Puntatori ovunque!
             return a->distanza < b->distanza;
         });
}

ordina_taxi(&mia_flotta);  // Devi ricordare &
```

**Dopo (con riferimenti):**
```cpp
void ordina_taxi(vector<Taxi>& flotta) {
    sort(flotta.begin(), flotta.end(),
         [](const Taxi& a, const Taxi& b) {  // Sintassi pulita!
             return a.distanza < b.distanza;
         });
}

ordina_taxi(mia_flotta);  // Chiamata naturale
```

**Stesso risultato, codice più leggibile e sicuro!**

## Riassunto

### Concetti Chiave
1. **Riferimenti = Alias** di variabili esistenti
2. **`&` solo nella dichiarazione** - poi sintassi normale
3. **`const&` per parametri grandi** - efficiente e sicuro
4. **Niente asterischi** - tutto più pulito
5. **Non NULL, sempre validi** - più sicuri dei puntatori

### Regola d'Oro
**"Se devi passare una struct/string/vector/container a una funzione, usa `const&` a meno che non devi modificarla (allora solo `&`)"**

### Progressione Naturale
1. **C**: Puntatori ovunque, `*` e `&` confusi
2. **C++ Base**: Stessi puntatori del C
3. **C++ Moderno**: Riferimenti per parametri, puntatori solo quando necessario

## Prossima Lezione
Organizzazione progetti: file multipli, header, namespace - costruiamo la nostra libreria!

---
*Lezione 5 completata - I riferimenti non hanno più segreti!*