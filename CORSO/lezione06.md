# Lezione 6: Organizzazione Progetti e Programmazione Modulare

## Obiettivo
Imparare a organizzare progetti C++ in file multipli, creare librerie personalizzate e strutturare codice professionale.

## Il Problema: Tutto in Un File

### Prima (Approccio Monolitico)
```cpp
// tutto_insieme.cpp - 200+ righe!
#include <iostream>
#include <vector>
#include <string>
using namespace std;

class Taxi {
    // ... 30 righe di codice classe
};

void trovaTaxiVicino() {
    // ... 20 righe di logica
}

void contaTaxiLiberi() {
    // ... 15 righe di logica  
}

int main() {
    // ... 50 righe di logica principale
}
```

**Problemi:**
- 🚫 **File gigantesco** - difficile da navigare
- 🚫 **Tutto mischiato** - classi + utilities + main
- 🚫 **Non riusabile** - non puoi usare Taxi in altri progetti
- 🚫 **Difficile debug** - errori si perdono nel marasma
- 🚫 **Collaborazione impossibile** - conflitti Git continui

## La Soluzione: Programmazione Modulare

### Architettura del Progetto
```
progetto_modulare/
├── main.cpp        ← Entry point (logica principale)
├── taxi.hpp        ← Dichiarazioni classe Taxi  
├── taxi.cpp        ← Implementazioni classe Taxi
├── utils.hpp       ← Dichiarazioni funzioni utilità
└── utils.cpp       ← Implementazioni funzioni utilità
```

**Vantaggi:**
- ✅ **Modularità** - ogni file ha uno scopo preciso
- ✅ **Riusabilità** - puoi usare Taxi in altri progetti
- ✅ **Manutenibilità** - modifiche isolate
- ✅ **Collaborazione** - team diversi su file diversi
- ✅ **Compilazione incrementale** - solo file modificati

## Concetti Fondamentali

### 1. Header Files (.hpp/.h)

#### Cosa Contengono
```cpp
// taxi.hpp - SOLO DICHIARAZIONI
#ifndef TAXI_HPP  // Header guard (spiegato dopo)
#define TAXI_HPP

#include <string>  // Dipendenze necessarie

class Taxi {
private:
    std::string id;     // Dichiarazione variabili membro
    std::string stato;
    double distanza;

public:
    // SOLO prototipi - niente implementazioni!
    Taxi(std::string id, std::string stato, double distanza);
    void stampaInfo();
    double getDistanza() const;
    std::string getStato() const;
    void setStato(std::string nuovoStato);
};

#endif // TAXI_HPP
```

#### Regole degli Header
1. **Solo dichiarazioni** - mai implementazioni complete
2. **Include minimal** - solo quello che serve per le dichiarazioni
3. **std:: esplicito** - mai `using namespace std` negli header
4. **Header guards** - sempre presenti
5. **Documentazione** - commenti per l'API pubblica

### 2. Source Files (.cpp)

#### Cosa Contengono
```cpp
// taxi.cpp - SOLO IMPLEMENTAZIONI
#include "taxi.hpp"  // Include il proprio header

// Ora puoi usare using namespace std (sicuro nei .cpp)
using namespace std;

// Implementazioni complete
Taxi::Taxi(string id, string stato, double distanza) 
    : id(id), stato(stato), distanza(distanza) {
    // Corpo del costruttore (può essere vuoto)
}

void Taxi::stampaInfo() {
    cout << "Taxi " << id << " - Stato: " << stato 
         << " - Distanza: " << distanza << " km" << endl;
}

double Taxi::getDistanza() const {
    return distanza;
}

string Taxi::getStato() const {
    return stato;
}

void Taxi::setStato(string nuovoStato) {
    stato = nuovoStato;
}
```

#### Regole dei Source Files
1. **Include proprio header** - sempre primo include
2. **Implementazioni complete** - tutta la logica qui
3. **using namespace OK** - sicuro nei file sorgente
4. **Sintassi Classe::metodo** - per definire implementazioni

### 3. Header Guards - Prevenire Inclusioni Multiple

#### Il Problema
```cpp
// file1.hpp
#include "taxi.hpp"

// file2.hpp
#include "taxi.hpp"

// main.cpp
#include "file1.hpp"
#include "file2.hpp"  // ERRORE! taxi.hpp incluso 2 volte!
```

**Risultato:** Classe `Taxi` definita multiple volte → errore compilazione

#### La Soluzione: Header Guards
```cpp
#ifndef TAXI_HPP     // "Se NON è definito TAXI_HPP"
#define TAXI_HPP     // "Definiscilo ora"

// ... tutto il contenuto del file ...

#endif // TAXI_HPP   // Fine del controllo
```

#### Come Funziona
1. **Prima inclusione:**
   - `#ifndef TAXI_HPP` → TRUE (non esiste)
   - `#define TAXI_HPP` → crea la "bandierina"
   - Processa tutto il contenuto
   
2. **Seconda inclusione:**
   - `#ifndef TAXI_HPP` → FALSE (esiste già!)
   - Salta tutto fino a `#endif`
   - **Nessuna duplicazione!**

#### Alternative Moderne
```cpp
#pragma once  // Alternativa più semplice (ma meno portabile)

// ... contenuto file ...
```

### 4. Il Costrutto const nei Metodi

#### Problema dei Metodi Getter
```cpp
void stampaFlotta(const vector<Taxi>& flotta) {
    for (const Taxi& taxi : flotta) {
        cout << taxi.getDistanza();  // ERRORE se getDistanza() non è const!
    }
}
```

#### Perché l'Errore?
- `const Taxi& taxi` = oggetto in sola lettura
- `getDistanza()` non const = "potrebbe modificare l'oggetto"
- Compilatore: "Non posso chiamare metodo modificante su oggetto const!"

#### La Soluzione: Metodi const
```cpp
// In taxi.hpp
double getDistanza() const;  // Prometto che non modifico l'oggetto

// In taxi.cpp  
double Taxi::getDistanza() const {
    return distanza;  // Solo lettura - OK!
    // distanza = 0;  // ERRORE! Non posso modificare in metodo const
}
```

#### Regola Generale
- **Getter (lettura)** → sempre `const`
- **Setter (modifica)** → mai `const`
- **Utility (calcoli)** → `const` se non modificano stato

### 5. Namespace - Organizzazione Logica

#### Il Problema dei Conflitti
```cpp
// In libreria A
void stampaInfo() { /* versione A */ }

// In libreria B  
void stampaInfo() { /* versione B */ }

// Nel tuo codice
stampaInfo();  // Quale versione? CONFLITTO!
```

#### La Soluzione: Namespace
```cpp
// utils.hpp
namespace TaxiSystem {
    void stampaIntestazione(const std::string& titolo);
    Taxi* trovaTaxiVicino(std::vector<Taxi>& flotta);
    int contaTaxiLiberi(const std::vector<Taxi>& flotta);
}

// utils.cpp
namespace TaxiSystem {
    void stampaIntestazione(const std::string& titolo) {
        std::cout << "\n=== " << titolo << " ===" << std::endl;
    }
    
    // ... altre implementazioni
}

// main.cpp
TaxiSystem::stampaIntestazione("FLOTTA");  // Esplicito!
```

#### Vantaggi Namespace
- ✅ **Nessun conflitto** - ogni funzione ha "cognome"
- ✅ **Organizzazione logica** - funzioni correlate insieme
- ✅ **API chiara** - sai da dove viene ogni funzione
- ✅ **Riusabilità** - puoi avere `MathUtils::somma()` e `StringUtils::somma()`

### 6. Puntatori nelle Funzioni di Ricerca

#### Perché Ritornare Puntatori?
```cpp
// METODO 1: Ritorna copia (LENTO)
Taxi trovaTaxiVicino(vector<Taxi>& flotta) {
    // ... trova taxi
    return flotta[indice];  // COPIA tutto l'oggetto!
}

// METODO 2: Ritorna puntatore (VELOCE)
Taxi* trovaTaxiVicino(vector<Taxi>& flotta) {
    // ... trova taxi
    return &flotta[indice];  // Solo 8 byte (indirizzo)!
}
```

#### Uso dei Puntatori Ritornati
```cpp
Taxi* vicino = TaxiSystem::trovaTaxiVicino(flotta);

if (vicino != nullptr) {  // Controlla se trovato
    vicino->stampaInfo();          // Usa -> per accedere ai metodi
    // oppure
    (*vicino).stampaInfo();        // Equivalente con *
    
    double dist = vicino->getDistanza();  // Getter con ->
}
```

#### Controllo Sicurezza
```cpp
Taxi* trovaTaxiVicino(vector<Taxi>& flotta) {
    if (flotta.empty()) return nullptr;  // Caso edge: flotta vuota
    
    // ... logica di ricerca
    return &flotta[indice_trovato];
}
```

### 7. Lambda Functions negli Algoritmi

#### Problema: Criteri di Confronto Personalizzati
```cpp
// Come dici a min_element di confrontare per distanza?
auto vicino = min_element(flotta.begin(), flotta.end(), ??? );
```

#### Soluzione: Lambda Function
```cpp
auto vicino = min_element(flotta.begin(), flotta.end(),
    [](const Taxi& a, const Taxi& b) {  // Lambda function
        return a.getDistanza() < b.getDistanza();
    });
```

#### Anatomia della Lambda
```cpp
[cattura](parametri) { corpo della funzione }
 ↑        ↑           ↑
 |        |           └─ Cosa fa
 |        └─ Input che riceve  
 └─ Variabili dall'esterno (vuoto = niente)
```

#### Lambda vs Funzione Normale
```cpp
// Metodo vecchio: funzione separata
bool confrontaDistanza(const Taxi& a, const Taxi& b) {
    return a.getDistanza() < b.getDistanza();
}
auto vicino = min_element(flotta.begin(), flotta.end(), confrontaDistanza);

// Metodo moderno: lambda inline
auto vicino = min_element(flotta.begin(), flotta.end(),
    [](const Taxi& a, const Taxi& b) {
        return a.getDistanza() < b.getDistanza();
    });
```

**Vantaggi Lambda:**
- ✅ **Inline** - logica vicino al punto d'uso
- ✅ **Concisa** - niente funzioni extra
- ✅ **Locale** - visibile solo dove serve

## Compilazione di Progetti Multipli

### Compilation Units
```cpp
// Ogni .cpp è una "compilation unit" separata
main.cpp     → main.o     (object file)
taxi.cpp     → taxi.o     (object file)  
utils.cpp    → utils.o    (object file)
```

### Processo di Build
```bash
# STEP 1: Compila ogni .cpp in .o (compilation)
g++ -c main.cpp   # Genera main.o
g++ -c taxi.cpp   # Genera taxi.o  
g++ -c utils.cpp  # Genera utils.o

# STEP 2: Collega tutti i .o in eseguibile (linking)
g++ main.o taxi.o utils.o -o taxi_system.exe

# OPPURE tutto in un comando
g++ main.cpp taxi.cpp utils.cpp -o taxi_system.exe
```

### Cosa Succede Durante la Compilazione
1. **Preprocessing** - Espande #include, #define
2. **Compilation** - .cpp → .o (codice macchina + simboli non risolti)
3. **Linking** - Risolve simboli, collega tutto

## Progetto Completo: Sistema Taxi Modulare

### Struttura File Finale
```
progetto_modulare/
├── taxi.hpp        ← Classe Taxi (dichiarazioni)
├── taxi.cpp        ← Classe Taxi (implementazioni)
├── utils.hpp       ← Utilities TaxiSystem (dichiarazioni)
├── utils.cpp       ← Utilities TaxiSystem (implementazioni)
└── main.cpp        ← Programma principale
```

### taxi.hpp - Header Completo
```cpp
#ifndef TAXI_HPP
#define TAXI_HPP

#include <string>
#include <iostream>

class Taxi {
private:
    std::string id;
    std::string stato;
    double distanza;

public:
    // Costruttore
    Taxi(std::string id, std::string stato, double distanza);
    
    // Metodi di interfaccia
    void stampaInfo();
    double getDistanza() const;        // const = non modifica oggetto
    std::string getStato() const;      // const = getter sicuro
    void setStato(std::string nuovoStato);
};

#endif // TAXI_HPP
```

### taxi.cpp - Implementazioni Complete
```cpp
#include "taxi.hpp"
using namespace std;

// Lista di inizializzazione - più efficiente
Taxi::Taxi(string id, string stato, double distanza) 
    : id(id), stato(stato), distanza(distanza) {
}

void Taxi::stampaInfo() {
    cout << "Taxi " << id << " - Stato: " << stato 
         << " - Distanza: " << distanza << " km" << endl;
}

double Taxi::getDistanza() const {
    return distanza;
}

string Taxi::getStato() const {
    return stato;
}

void Taxi::setStato(string nuovoStato) {
    stato = nuovoStato;
}
```

### utils.hpp - Libreria di Utilità
```cpp
#ifndef UTILS_HPP
#define UTILS_HPP

#include "taxi.hpp"
#include <vector>

namespace TaxiSystem {
    
    // Trova il taxi più vicino in una flotta
    Taxi* trovaTaxiVicino(std::vector<Taxi>& flotta);
    
    // Conta taxi con stato specifico
    int contaTaxiLiberi(const std::vector<Taxi>& flotta);
    
    // Utility di presentazione
    void stampaIntestazione(const std::string& titolo);
    
} // namespace TaxiSystem

#endif // UTILS_HPP
```

### utils.cpp - Implementazioni Algoritmi
```cpp
#include "utils.hpp"
#include <algorithm>
using namespace std;

namespace TaxiSystem {
    
    Taxi* trovaTaxiVicino(vector<Taxi>& flotta) {
        if (flotta.empty()) return nullptr;
        
        // Usa algoritmo STL con lambda
        auto vicino = min_element(flotta.begin(), flotta.end(),
            [](const Taxi& a, const Taxi& b) {
                return a.getDistanza() < b.getDistanza();
            });
        
        return &(*vicino);  // Converte iteratore in puntatore
    }
    
    int contaTaxiLiberi(const vector<Taxi>& flotta) {
        int count = 0;
        for (const auto& taxi : flotta) {
            if (taxi.getStato() == "libero") {
                count++;
            }
        }
        return count;
    }
    
    void stampaIntestazione(const string& titolo) {
        cout << "\n=== " << titolo << " ===" << endl;
    }

} // namespace TaxiSystem
```

### main.cpp - Orchestrazione Finale
```cpp
#include <iostream>
#include <vector>
#include "taxi.hpp"
#include "utils.hpp"
using namespace std;

int main() {
    // Crea flotta usando costruttori
    vector<Taxi> flotta = {
        Taxi("TX001", "libero", 2.5),
        Taxi("TX002", "occupato", 1.8),
        Taxi("TX003", "libero", 3.2)
    };
    
    // Usa funzioni del namespace TaxiSystem
    TaxiSystem::stampaIntestazione("FLOTTA TAXI");
    for (auto& taxi : flotta) {
        taxi.stampaInfo();
    }
    
    // Statistiche flotta
    int liberi = TaxiSystem::contaTaxiLiberi(flotta);
    cout << "\nTaxi liberi: " << liberi << endl;
    
    // Modifica stato
    TaxiSystem::stampaIntestazione("AGGIORNA STATO");
    flotta[0].setStato("occupato");
    cout << "Primo taxi aggiornato: ";
    flotta[0].stampaInfo();
    
    // Trova ottimale
    TaxiSystem::stampaIntestazione("TAXI PIU' VICINO");
    Taxi* vicino = TaxiSystem::trovaTaxiVicino(flotta);
    if (vicino != nullptr) {
        cout << "Il piu' vicino e': ";
        vicino->stampaInfo();
    }
    
    return 0;
}
```

## Compilazione e Esecuzione

### Comando Build
```bash
g++ main.cpp taxi.cpp utils.cpp -o taxi_system.exe
```

### Output Atteso
```
=== FLOTTA TAXI ===
Taxi TX001 - Stato: libero - Distanza: 2.5 km
Taxi TX002 - Stato: occupato - Distanza: 1.8 km
Taxi TX003 - Stato: libero - Distanza: 3.2 km

Taxi liberi: 2

=== AGGIORNA STATO ===
Primo taxi aggiornato: Taxi TX001 - Stato: occupato - Distanza: 2.5 km

=== TAXI PIU' VICINO ===
Il piu' vicino e': Taxi TX002 - Stato: occupato - Distanza: 1.8 km
```

## Vantaggi dell'Approccio Modulare

### Per lo Sviluppatore
- 🧹 **Codice pulito** - ogni file ha responsabilità specifica
- 🔍 **Debug facile** - errori localizzati
- 🔄 **Riusabilità** - classi utilizzabili in progetti diversi
- 📈 **Scalabilità** - aggiungi nuovi moduli senza toccare esistenti

### Per il Team
- 👥 **Collaborazione** - sviluppatori su file diversi
- 🔀 **Git friendly** - conflitti minimizzati
- 📋 **Code review** - modifiche isolate e chiare
- 🧪 **Testing** - test unitari per singoli moduli

### Per le Performance
- ⚡ **Compilazione incrementale** - solo file modificati
- 💾 **Linking ottimizzato** - unused code elimination
- 🏗️ **Modularità runtime** - caricamento lazy possibile

## Best Practices Professionali

### 1. Naming Conventions
```cpp
// File
snake_case.hpp         // taxi_manager.hpp
PascalCase.hpp         // TaxiManager.hpp (alternativa)

// Classi  
PascalCase             // class TaxiManager

// Variabili/Funzioni
camelCase              // void findClosestTaxi()
snake_case             // void find_closest_taxi() (alternativa)

// Constanti
SCREAMING_SNAKE_CASE   // const int MAX_TAXI_COUNT = 100;

// Namespace
PascalCase             // namespace TaxiSystem
```

### 2. Organizzazione Directory
```
progetto_grande/
├── include/           ← Tutti i .hpp
│   ├── taxi.hpp
│   └── utils.hpp
├── src/               ← Tutti i .cpp  
│   ├── taxi.cpp
│   ├── utils.cpp
│   └── main.cpp
├── tests/             ← Unit tests
├── docs/              ← Documentazione
└── build/             ← File compilati
```

### 3. Include Guards Consistenti
```cpp
// Convenzione: PROGETTO_FILE_HPP
#ifndef TAXI_SYSTEM_TAXI_HPP
#define TAXI_SYSTEM_TAXI_HPP
// ... contenuto
#endif // TAXI_SYSTEM_TAXI_HPP
```

### 4. Documentazione Header
```cpp
/**
 * @file taxi.hpp
 * @brief Gestione entità Taxi per sistema di trasporto
 * @author Il Tuo Nome
 * @date 2025-01-XX
 */

#ifndef TAXI_HPP
#define TAXI_HPP

/**
 * @class Taxi
 * @brief Rappresenta un singolo taxi nella flotta
 * 
 * La classe Taxi incapsula tutte le informazioni e operazioni
 * relative a un veicolo taxi: identificativo, stato operativo
 * e posizione geografica.
 */
class Taxi {
    // ...
};
```

## Errori Comuni e Soluzioni

### 1. Circular Includes
```cpp
// ERRORE: a.hpp include b.hpp, b.hpp include a.hpp
// a.hpp
#include "b.hpp"
class A { B member; };

// b.hpp  
#include "a.hpp"
class B { A member; };
```

**Soluzione: Forward Declarations**
```cpp
// a.hpp
class B;  // Forward declaration
class A { B* member; };  // Usa puntatore

// b.hpp
#include "a.hpp" 
class B { A member; };
```

### 2. Include Mancanti
```cpp
// ERRORE: usi string ma non includi <string>
class Taxi {
    string id;  // ERRORE! string non definito
};
```

**Soluzione: Include Espliciti**
```cpp
#include <string>  // SEMPRE include le dipendenze
class Taxi {
    std::string id;  // OK!
};
```

### 3. using namespace in Header
```cpp
// ERRORE: inquina namespace globale
// taxi.hpp
using namespace std;  // ❌ NO negli header!
class Taxi {
    string id;  // Possibili conflitti
};
```

**Soluzione: std:: Esplicito**
```cpp
// taxi.hpp
#include <string>
class Taxi {
    std::string id;  // ✅ Sempre esplicito negli header
};
```

## Prossimi Passi: Espansione del Sistema

### Funzionalità da Aggiungere
1. **Classe Coordinate** - GPS latitude/longitude
2. **Classe Flotta** - container + operazioni aggregate  
3. **File I/O** - salvataggio/caricamento stato flotta
4. **Configurazione** - parametri sistema da file
5. **Logging** - tracciamento operazioni

### Architettura Futura
```
taxi_system/
├── core/              ← Logica business
│   ├── taxi.hpp/cpp
│   ├── flotta.hpp/cpp
│   └── coordinate.hpp/cpp
├── utils/             ← Utilities
│   ├── file_io.hpp/cpp
│   ├── logger.hpp/cpp
│   └── config.hpp/cpp
├── ui/                ← Interfaccia utente
│   └── console_ui.hpp/cpp
└── main.cpp           ← Entry point
```

## Riassunto Concetti Chiave

### Fondamentali Acquisiti
1. **Separazione Responsabilità** - ogni file uno scopo
2. **Header Guards** - prevenzione inclusioni multiple
3. **const Correctness** - metodi che non modificano stato
4. **Namespace** - organizzazione logica funzioni
5. **Puntatori Return** - efficienza nelle ricerche
6. **Lambda Functions** - criteri personalizzati inline
7. **Compilazione Modulare** - gestione progetti complessi

### Skills Professionali
- ✅ **Project Architecture** - strutturare codice scalabile
- ✅ **API Design** - interfacce pubbliche pulite
- ✅ **Code Organization** - modularità e riusabilità
- ✅ **Build Systems** - compilazione file multipli
- ✅ **Best Practices** - convenzioni industria

### Preparazione per OOP Avanzato
- 🎯 **Encapsulation** - controllo accesso membri
- 🎯 **Constructor Patterns** - inizializzazione oggetti
- 🎯 **Method Design** - interfacce pubbliche vs implementazione
- 🎯 **Memory Management** - ownership e lifetime oggetti

## Prossima Lezione
Consolidamento pratico: costruiamo insieme un sistema di gestione biblioteca usando architettura modulare - classi Libro, Utente, Biblioteca con operazioni complete!

---
*Lezione 6 completata - Sei ufficialmente un Project Architect! 🏗️*
