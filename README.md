# ⚙️ JavaEE-Middleware-Architecture
> **Implementazione di un'infrastruttura distribuita per la gestione asincrona di asset aziendali.**

![Java](https://img.shields.io/badge/Java-Enterprise-ED8B00?style=for-the-badge&logo=java&logoColor=white)
![Middleware](https://img.shields.io/badge/Layer-Middleware-red?style=for-the-badge)
![EJB](https://img.shields.io/badge/Jakarta-EJB%20%2F%20JPA-blue?style=for-the-badge)

---

## 🏛️ Architettura del Sistema
Questo progetto analizza e implementa i pattern fondamentali dell'architettura **Middleware** in ambiente Java/Jakarta EE. L'obiettivo è dimostrare come gestire flussi di dati complessi tra client distribuiti e database, garantendo transazionalità e comunicazione asincrona.

### 🛠️ Core Stack & Logic
Il sistema è suddiviso in componenti modulari che riflettono le best practice enterprise:

* **Logic Layer (EJB):** Session Beans stateless per l'elaborazione dei dati e Singleton per il controllo del ciclo di vita all'avvio.
* **Data Layer (JPA):** Gestione della persistenza tramite Object-Relational Mapping (ORM) con focus sull'ottimizzazione delle query.
* **Messaging Layer (JMS):** Implementazione di un sistema di messaggistica asincrona tramite MDB (Message-Driven Beans) per il disaccoppiamento dei processi.
* **Integration Layer (SOAP):** Esposizione di endpoint Web Service per l'interoperabilità con sistemi eterogenei.

---

## 📂 Organizzazione dei Moduli
Per facilitare la revisione del codice, i componenti sono stati isolati per responsabilità:

- **`Entity/`**: Modelli di dati con annotazioni JPA e vincoli di integrità.
- **`EJB/`**: Logica di business e gestione delle transazioni.
- **`MDB/`**: Consumer asincroni per eventi JMS.
- **`Clients/`**: Implementazioni di client remoti e interfacce di test Web Service.



---

## 📂 Struttura del progetto (Project Structure)
L'intera soluzione è logicamente e fisicamente suddivisa in tre macro-progetti indipendenti, che simulano un ambiente di produzione reale:

### 1. 🖥️ `Server` (The Core)
Rappresenta il **Backend Enterprise**. È il cuore del middleware dove risiede la persistenza e la logica decisionale.
* **Business Logic (EJB Stateless):** Gestisce le transazioni e garantisce thread-safety nell'accesso ai dati.
* **Data Persistence (JPA):** Implementa il mapping ORM tra oggetti Java e il database relazionale. Utilizza `NamedQueries` pre-compilate per ottimizzare le prestazioni.
* **Messaging (MDB):** Implementa un `MessageDrivenBean` che ascolta in modo non bloccante su un Topic JMS, permettendo aggiornamenti di stato asincroni.
* **Service Exposure:** Pubblica i metodi di business sia tramite interfacce `@Remote` (per i client Java) sia tramite endpoint `@WebService` (SOAP).

### 2. 📱 `Client` (RMI & JMS Producer)
Un client "thick" (pesante) che interagisce con il server utilizzando protocolli nativi Java.
* **JNDI Lookup:** Dimostra la capacità di localizzare servizi remoti nel Global Context dell'Application Server.
* **JMS Integration:** Funge da *Producer*. Invia messaggi strutturati al server per segnalare eventi (es. la vendita di un asset) senza attendere la risposta del database, migliorando la reattività dell'interfaccia utente.

### 3. 🌐 `WSClient` (Interoperability Client)
Un client leggero che simula l'integrazione con sistemi di terze parti o legacy.
* **SOAP Protocol:** Utilizza il protocollo XML-based per interrogare il server.
* **Service Independence:** Non necessita delle classi del server per funzionare, dimostrando come il middleware possa comunicare con qualsiasi tecnologia esterna tramite standard aperti.

---

## 🚀 Punti di Forza Tecnici
1.  **Resilienza:** Gestione avanzata delle eccezioni nei processi asincroni per evitare il rollback dell'intero sistema.
2.  **Transazionalità:** Utilizzo di `@PersistenceContext` e gestione automatica delle transazioni tipica degli EJB.
3.  **Versatilità:** Sebbene l'attuale implementazione utilizzi un dominio di "inventory", l'intera architettura è progettata per essere agnostica e scalabile su qualsiasi dominio di business.
