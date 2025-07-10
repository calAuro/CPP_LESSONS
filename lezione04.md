# Lezione 4: Algoritmi STL e Lambda

## Obiettivo
Eliminare i loop manuali utilizzando gli algoritmi STL e le funzioni lambda.

## Dal C al C++: Algoritmi

### Problema in C
```c
// Bubble sort manuale - tante righe di codice
for (int i = 0; i < n-1; i++) {
    for (int j = 0; j < n-i-1; j++) {
        if (arr[j] > arr[j+1]) {
            temp = arr[j];
            arr[j] = arr[j+1];
            arr[j+1] = temp;
        }
    }
}
```

### Soluzione C++
```cpp
#include <algorithm>
sort(numeri.begin(), numeri.end());  // Una riga!
```

## La Libreria `<algorithm>`

### Algoritmi Principali
- **`sort()`** - ordinamento ottimizzato
- **`find()`** - ricerca elemento
- **`count()`** - conta occorrenze specifiche
- **`count_if()`** - conta elementi che soddisfano condizione
- **`min_element()`** - trova elemento minimo
- **`max_element()`** - trova elemento massimo
- **`transform()`** - applica funzione a tutti gli elementi
- **`reverse()`** - inverte ordine elementi

## 1. SORT - Ordinamento

### Prima Impara la Logica: Bubble Sort in C++
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<int> numeri = {64, 34, 25, 12, 22, 11, 90};
    int n = numeri.size();
    int temp;
    
    cout << "Array originale: ";
    for (int num : numeri) {
        cout << num << " ";
    }
    cout << endl;
    
    // BUBBLE SORT - logica identica al C
    for (int i = 0; i < n-1; i++) {
        for (int j = 0; j < n-i-1; j++) {
            if (numeri[j] > numeri[j+1]) {
                // Swap manuale
                temp = numeri[j];
                numeri[j] = numeri[j+1];
                numeri[j+1] = temp;
            }
        }
    }
    
    cout << "Array ordinato: ";
    for (int num : numeri) {
        cout << num << " - ";
    }
    
    return 0;
}
```

### Poi Usa la Scorciatoia STL
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<int> numeri = {64, 34, 25, 12, 22, 11, 90};
    
    cout << "Prima: ";
    for (int n : numeri) {
        cout << n << " ";
    }
    cout << endl;
    
    // Ordinamento STL - una riga!
    sort(numeri.begin(), numeri.end());
    
    cout << "Dopo: ";
    for (int n : numeri) {
        cout << n << " ";
    }
    cout << endl;
    
    return 0;
}
```

## 2. FIND - Ricerca

### Ricerca Manuale vs STL
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<int> numeri = {10, 20, 30, 40, 50};
    int cerca = 30;
    int posizione = -1;
    
    // RICERCA MANUALE (come in C)
    for (int i = 0; i < numeri.size(); i++) {
        if (numeri[i] == cerca) {
            posizione = i;
            break;
        }
    }
    
    if (posizione != -1) {
        cout << "Manuale: trovato in posizione " << posizione << endl;
    }
    
    // RICERCA STL
    auto risultato = find(numeri.begin(), numeri.end(), cerca);
    if (risultato != numeri.end()) {
        int pos_stl = risultato - numeri.begin();
        cout << "STL: trovato in posizione " << pos_stl << endl;
    }
    
    return 0;
}
```

## 3. Le Funzioni Lambda

### Concetto
Le lambda sono "funzioni senza nome" che scrivi direttamente dove servono.

### Sintassi Base
```cpp
[cattura](parametri) { corpo della funzione }
```

### Esempi Progressivi
```cpp
// Funzione normale
int raddoppia(int x) {
    return x * 2;
}

// Stessa cosa con lambda
auto lambda_raddoppia = [](int x) { return x * 2; };

// Uso diretto
int risultato = lambda_raddoppia(5);  // risultato = 10
```

### Lambda con Cattura
```cpp
int moltiplicatore = 3;
auto lambda_moltiplica = [moltiplicatore](int x) { 
    return x * moltiplicatore; 
};

int risultato = lambda_moltiplica(4);  // risultato = 12
```

## 4. TRANSFORM - Trasformazione

### Esempio Base: Raddoppiare Numeri
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<int> numeri = {1, 2, 3, 4, 5};
    vector<int> raddoppiati(5);  // Vector di destinazione
    
    cout << "Originali: ";
    for (int n : numeri) {
        cout << n << " ";
    }
    cout << endl;
    
    // TRANSFORM: applica lambda a ogni elemento
    transform(numeri.begin(), numeri.end(), raddoppiati.begin(), 
              [](int x) { return x * 2; });
    
    cout << "Raddoppiati: ";
    for (int n : raddoppiati) {
        cout << n << " ";
    }
    cout << endl;
    
    return 0;
}
```

### Parametri di Transform
```cpp
transform(inizio_sorgente, fine_sorgente, inizio_destinazione, funzione);
```

### Esempio Pratico: Normalizzazione Quiz
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<double> punteggi_quiz = {42, 33, 51, 28, 45, 38};  // su 56
    vector<double> voti_normalizzati(6);
    
    cout << "Punteggi quiz (0-56): ";
    for (double p : punteggi_quiz) {
        cout << p << " ";
    }
    cout << endl;
    
    // NORMALIZZAZIONE: da 0-56 a 0-10
    transform(punteggi_quiz.begin(), punteggi_quiz.end(),
              voti_normalizzati.begin(),
              [](double punteggio) { return (punteggio / 56.0) * 10.0; });
    
    cout << "Voti normalizzati (0-10): ";
    for (double v : voti_normalizzati) {
        cout << v << " ";
    }
    cout << endl;
    
    return 0;
}
```

**Formula generale:** `voto_normalizzato = (punteggio / max_punteggio) * scala_target`

## 5. COUNT_IF - Conteggio Condizionale

### Sintassi
```cpp
count_if(inizio, fine, condizione);
```

### Esempio: Contare Elementi in Range
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<double> distanze_taxi = {0.8, 2.5, 1.2, 3.8, 0.5, 4.2, 1.8};
    double raggio_zona = 2.0;
    
    // Conta taxi entro 2 km
    int taxi_in_zona = count_if(distanze_taxi.begin(), 
                                distanze_taxi.end(),
                                [raggio_zona](double dist) {
                                    return dist <= raggio_zona;
                                });
    
    cout << "Taxi disponibili entro " << raggio_zona << " km: ";
    cout << taxi_in_zona << endl;
    
    return 0;
}
```

### Confronto con Loop Manuale
```cpp
// METODO MANUALE (senza count_if)
int taxi_in_zona = 0;
for (int i = 0; i < distanze_taxi.size(); i++) {
    if (distanze_taxi[i] <= raggio_zona) {
        taxi_in_zona++;
    }
}

// Stesso risultato, ma 5 righe vs 1 riga con count_if
```

## 6. MIN_ELEMENT - Trova Minimo

### Trova Minimo con Condizione Personalizzata
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

struct Taxi {
    string id;
    string stato;
    double distanza;
};

int main() {
    vector<Taxi> flotta = {
        {"TX001", "libero", 2.5},
        {"TX002", "occupato", 1.2}, 
        {"TX003", "libero", 0.8},
        {"TX004", "pausa", 3.8}
    };
    
    // Trova il taxi più vicino
    auto taxi_vicino = min_element(flotta.begin(), flotta.end(),
                                   [](const Taxi& a, const Taxi& b) {
                                       return a.distanza < b.distanza;
                                   });
    
    cout << "Taxi più vicino: " << taxi_vicino->id;
    cout << " - Distanza: " << taxi_vicino->distanza << " km" << endl;
    
    return 0;
}
```

**Nota:** `const Taxi& a, const Taxi& b` - vedremo il significato di `&` nella prossima lezione!

## Applicazioni Pratiche: Progetto Taxi

### Vector di Struct per Dati Organizzati
```cpp
struct Taxi {
    string id;
    string stato;
    double distanza;
};

vector<Taxi> flotta = {
    {"TX001", "libero", 0.8},
    {"TX002", "occupato", 2.5},
    {"TX003", "libero", 1.2}
};
```

### Operazioni Dinamiche
```cpp
// Aggiungere taxi
flotta.push_back({"TX004", "libero", 1.5});

// Modificare stato
flotta[0].stato = "occupato";

// Rimuovere ultimo
flotta.pop_back();
```

### Algoritmi per Gestione Flotta
```cpp
// 1. Conta taxi liberi
int liberi = count_if(flotta.begin(), flotta.end(),
                      [](const Taxi& t) { return t.stato == "libero"; });

// 2. Trova più vicino tra quelli liberi
auto vicino = min_element(flotta.begin(), flotta.end(),
                          [](const Taxi& a, const Taxi& b) {
                              if (a.stato == "libero" && b.stato == "libero")
                                  return a.distanza < b.distanza;
                              return a.stato == "libero";
                          });

// 3. Ordina per distanza
sort(flotta.begin(), flotta.end(),
     [](const Taxi& a, const Taxi& b) {
         return a.distanza < b.distanza;
     });
```

## Vantaggi STL vs Loop Manuali

| Aspetto | Loop Manuale | Algoritmi STL |
|---------|--------------|---------------|
| **Righe di codice** | Molte (5-10+) | Poche (1-3) |
| **Leggibilità** | Logica dispersa | Intento chiaro |
| **Errori** | Possibili (indici, condizioni) | Minimizzati |
| **Ottimizzazione** | Dipende da te | Automatica |
| **Manutenzione** | Complessa | Semplificata |

## Progressione degli Algoritmi

### Complessità Crescente
1. **`sort()`** - ordinamento semplice
2. **`find()`** - ricerca valore specifico  
3. **`count_if()`** - conteggio con condizione
4. **`transform()`** - trasformazione elementi
5. **`min_element()`** - ricerca con criterio personalizzato

### Come Funziona min_element()
```
Processo interno per trovare il minimo:
1. candidato_minimo = primo elemento
2. Per ogni elemento successivo:
   - Confronta con candidato_minimo usando la lambda
   - Se "minore", diventa nuovo candidato_minimo
3. Ritorna il candidato_minimo finale
```

## Prossima Lezione
Puntatori e Riferimenti - capiamo il significato di quel `&` che abbiamo visto nelle lambda!

---
*Salvato il: 10-07-2025*
