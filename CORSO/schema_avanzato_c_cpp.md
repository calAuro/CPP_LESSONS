# Schema Comparativo: C vs C++ vs C++ Moderno

| **COSTRUTTO** | **ESEMPIO C** | **ESEMPIO C++** | **C++ MODERNO (FURBO)** | **NOTE** |
|---------------|---------------|-----------------|-------------------------|----------|
| **Array Statici** | `int arr[5] = {1,2,3,4,5};` | `int arr[5] = {1,2,3,4,5};` | `array<int,5> arr = {1,2,3,4,5};` | C++ identico. `std::array` ha metodi `.size()`, `.at()` |
| **Array Dinamici** | `int* arr = malloc(n * sizeof(int)); free(arr);` | `int* arr = new int[n]; delete[] arr;` | `vector<int> arr(n);` | Vector gestisce memoria automaticamente |
| **Stringhe** | `char str[100]; strcpy(str, "hello");` | `char str[100]; strcpy(str, "hello");` | `string str = "hello";` | String ridimensiona automaticamente |
| **Struct Base** | `struct Point { int x, y; };` | `struct Point { int x, y; };` | `struct Point { int x, y; Point(int x, int y) : x(x), y(y) {} };` | C++ identico. Moderno aggiunge costruttori |
| **Liste Collegate** | Struct con puntatori + malloc/free per gestione nodi | Struct con puntatori + new/delete per gestione nodi | `list<int> lista; lista.push_back(10);` | STL list è doppiamente collegata |
| **Stack/Pila** | Struct con array/puntatori + implementazione push/pop manuale | Struct con array/puntatori + implementazione push/pop manuale | `stack<int> pila; pila.push(10);` | Container STL pronto all'uso |
| **Queue/Coda** | Struct con array circolare + gestione front/rear manuale | Struct con array circolare + gestione front/rear manuale | `queue<int> coda; coda.push(10);` | Container STL con front(), back() |
| **Grafi - Lista Adiacenza** | Struct con array di puntatori + malloc per ogni nodo | Struct con array di puntatori + new/delete per ogni nodo | `vector<vector<int>> grafo;` oppure `map<int, vector<int>> adj;` | Vector 2D o map per flessibilità |
| **Grafi - Matrice Adiacenza** | Allocazione manuale matrice 2D con malloc + doppio loop | Allocazione manuale matrice 2D con new + doppio loop | `vector<vector<int>> matrix(n, vector<int>(n, 0));` | Vector 2D elimina gestione manuale |
| **Alberi Binari** | Struct TreeNode con left/right + malloc/free per nodi | Struct TreeNode con left/right + new/delete per nodi | `struct TreeNode { int data; unique_ptr<TreeNode> left, right; };` | Smart pointers gestiscono memoria |
| **Hash Table/Mappa** | Struct con array di bucket + hash function + gestione collisioni | Struct con array di bucket + hash function + gestione collisioni | `map<string, int> mappa; mappa["chiave"] = valore;` oppure `unordered_map` | Map usa alberi, unordered_map hash |
| **Passaggio Parametri** | `void func(int* arr, int size) { /* modifica *arr */ }` | `void func(int* arr, int size) { /* modifica *arr */ }` | `void func(vector<int>& arr) { /* modifica arr */ }` | Riferimenti eliminano * e & |
| **Ordinamento** | Implementazione manuale (bubble sort, etc.) con doppio loop | Implementazione manuale (bubble sort, etc.) con doppio loop | `sort(vec.begin(), vec.end());` | STL algoritmi ottimizzati |
| **Ricerca** | Loop manuale con confronto elemento per elemento | Loop manuale con confronto elemento per elemento | `auto it = find(vec.begin(), vec.end(), key);` | STL find con iteratori |
| **File I/O** | `FILE* fp = fopen("file.txt", "r"); fscanf(fp, "%d", &num); fclose(fp);` | `FILE* fp = fopen("file.txt", "r"); fscanf(fp, "%d", &num); fclose(fp);` | `ifstream file("file.txt"); file >> num;` | Stream C++ più sicuri, chiusura automatica |

## Esempi di Codice Dettagliati

### Array Dinamici: Da Malloc a Vector

```c
// C - Gestione manuale completa
int* crea_array(int dimensione) {
    int* arr = malloc(dimensione * sizeof(int));
    if (arr == NULL) {
        printf("Errore allocazione!\n");
        return NULL;
    }
    return arr;
}

void libera_array(int* arr) {
    free(arr);  // OBBLIGATORIO!
}

int main() {
    int* numeri = crea_array(5);
    for(int i = 0; i < 5; i++) {
        numeri[i] = i * 10;
    }
    libera_array(numeri);
    return 0;
}
```

```cpp
// C++ Base - new/delete
int* crea_array(int dimensione) {
    int* arr = new int[dimensione];
    return arr;
}

void libera_array(int* arr) {
    delete[] arr;  // OBBLIGATORIO!
}

int main() {
    int* numeri = crea_array(5);
    for(int i = 0; i < 5; i++) {
        numeri[i] = i * 10;
    }
    libera_array(numeri);
    return 0;
}
```

```cpp
// C++ Moderno - Vector (automatic!)
int main() {
    vector<int> numeri(5);  // Tutto automatico!
    for(int i = 0; i < 5; i++) {
        numeri[i] = i * 10;
    }
    // Niente delete[] - si pulisce da solo!
    return 0;
}
```

### Liste Collegate: Manual vs STL

```c
// C - Implementazione completa manuale
struct Nodo {
    int dato;
    struct Nodo* successivo;
};

struct Lista {
    struct Nodo* testa;
};

void inserisci(struct Lista* lista, int valore) {
    struct Nodo* nuovo = malloc(sizeof(struct Nodo));
    nuovo->dato = valore;
    nuovo->successivo = lista->testa;
    lista->testa = nuovo;
}

void stampa(struct Lista* lista) {
    struct Nodo* corrente = lista->testa;
    while(corrente != NULL) {
        printf("%d -> ", corrente->dato);
        corrente = corrente->successivo;
    }
    printf("NULL\n");
}

void libera_lista(struct Lista* lista) {
    struct Nodo* corrente = lista->testa;
    while(corrente != NULL) {
        struct Nodo* temp = corrente;
        corrente = corrente->successivo;
        free(temp);
    }
}
```

```cpp
// C++ Base - Stessa logica, sintassi leggermente diversa
struct Nodo {
    int dato;
    Nodo* successivo;  // No "struct" necessario
};

class Lista {
private:
    Nodo* testa;
public:
    Lista() : testa(nullptr) {}
    
    void inserisci(int valore) {
        Nodo* nuovo = new Nodo;
        nuovo->dato = valore;
        nuovo->successivo = testa;
        testa = nuovo;
    }
    
    void stampa() {
        Nodo* corrente = testa;
        while(corrente != nullptr) {
            cout << corrente->dato << " -> ";
            corrente = corrente->successivo;
        }
        cout << "NULL" << endl;
    }
    
    ~Lista() {  // Distruttore automatico
        while(testa) {
            Nodo* temp = testa;
            testa = testa->successivo;
            delete temp;
        }
    }
};
```

```cpp
// C++ Moderno - Una riga!
int main() {
    list<int> mia_lista;
    
    mia_lista.push_front(10);
    mia_lista.push_front(20);
    mia_lista.push_front(30);
    
    for(auto valore : mia_lista) {
        cout << valore << " -> ";
    }
    cout << "NULL" << endl;
    
    // Gestione memoria automatica!
    return 0;
}
```

### Hash Table vs Map

```c
// C - Hash Table completa (versione semplificata)
#define TABLE_SIZE 100

struct Entry {
    char* key;
    int value;
    struct Entry* next;  // Per gestire collisioni
};

struct HashTable {
    struct Entry* buckets[TABLE_SIZE];
};

unsigned int hash(char* key) {
    unsigned int hash_value = 0;
    while(*key) {
        hash_value = hash_value * 31 + *key;
        key++;
    }
    return hash_value % TABLE_SIZE;
}

void insert(struct HashTable* table, char* key, int value) {
    unsigned int index = hash(key);
    struct Entry* new_entry = malloc(sizeof(struct Entry));
    new_entry->key = malloc(strlen(key) + 1);
    strcpy(new_entry->key, key);
    new_entry->value = value;
    new_entry->next = table->buckets[index];
    table->buckets[index] = new_entry;
}

int get(struct HashTable* table, char* key) {
    unsigned int index = hash(key);
    struct Entry* entry = table->buckets[index];
    while(entry) {
        if(strcmp(entry->key, key) == 0) {
            return entry->value;
        }
        entry = entry->next;
    }
    return -1;  // Non trovato
}
```

```cpp
// C++ Base - Stessa implementazione con sintassi C++
class HashTable {
private:
    static const int TABLE_SIZE = 100;
    
    struct Entry {
        string key;
        int value;
        Entry* next;
        Entry(string k, int v) : key(k), value(v), next(nullptr) {}
    };
    
    Entry* buckets[TABLE_SIZE];
    
    unsigned int hash(string key) {
        unsigned int hash_value = 0;
        for(char c : key) {
            hash_value = hash_value * 31 + c;
        }
        return hash_value % TABLE_SIZE;
    }
    
public:
    HashTable() {
        for(int i = 0; i < TABLE_SIZE; i++) {
            buckets[i] = nullptr;
        }
    }
    
    void insert(string key, int value) {
        unsigned int index = hash(key);
        Entry* new_entry = new Entry(key, value);
        new_entry->next = buckets[index];
        buckets[index] = new_entry;
    }
    
    int get(string key) {
        unsigned int index = hash(key);
        Entry* entry = buckets[index];
        while(entry) {
            if(entry->key == key) {
                return entry->value;
            }
            entry = entry->next;
        }
        return -1;
    }
};
```

```cpp
// C++ Moderno - Due righe!
int main() {
    map<string, int> tabella;  // Oppure unordered_map per hash
    
    tabella["Mario"] = 28;
    tabella["Luigi"] = 25;
    
    cout << "Voto Mario: " << tabella["Mario"] << endl;
    
    // Gestione memoria e collisioni automatica!
    return 0;
}
```

## Progressione degli Shock 🤯

### Livello 1: Comfort Zone
```c
// "Questo lo so fare!"
int arr[] = {1, 2, 3, 4, 5};
for(int i = 0; i < 5; i++) {
    printf("%d ", arr[i]);
}
```

### Livello 2: Piccolo Upgrade
```cpp
// "Ok, cout invece di printf"
int arr[] = {1, 2, 3, 4, 5};
for(int i = 0; i < 5; i++) {
    cout << arr[i] << " ";
}
```

### Livello 3: Mind Blown 💥
```cpp
// "COSA?! È TUTTO QUI?!"
vector<int> arr = {1, 2, 3, 4, 5};
for(auto n : arr) cout << n << " ";
```

### Livello 4: Complete Shock 🌪️
```cpp
// Da 50 righe di hash table C a...
map<string, int> tabella;
tabella["chiave"] = 42;
cout << tabella["chiave"];
// "Ma questo è illegale!" 😱
```

## Vantaggi Scaletta per Spiegare

| Aspetto | C Manual | C++ Base | C++ Moderno | Impatto |
|---------|----------|----------|-------------|---------|
| **Righe di codice** | 50+ | 30+ | 3-5 | 🤯 |
| **Gestione memoria** | Manuale | Manuale | Automatica | 💪 |
| **Sicurezza** | Bassa | Media | Alta | 🛡️ |
| **Velocità sviluppo** | Lenta | Media | Velocissima | 🚀 |
| **Possibilità errori** | Molte | Alcune | Pochissime | ✅ |

**Il momento "EUREKA!"** quando capiscono che possono concentrarsi sulla **logica** invece che sulla **meccanica**! 🎯