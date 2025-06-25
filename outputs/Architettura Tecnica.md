# Output da: Solution Architect

**Obiettivo:** Definire un'architettura tecnica efficace per il progetto

**Task:** A partire dal Documento di Specifiche di Progetto definito dal Project Manager, progetta un'architettura software adatta per il progetto "{project_name}", includendo frontend, backend e database.


---

# Documento SAD per "ToDo List per la gestione di attività personali"

### 1. Obiettivo del documento
Questo documento SAD (Software Architecture Document) definisce l'architettura tecnica per la web app "ToDo List per la gestione di attività personali". Lo scopo è garantire che il sistema sia conforme alle specifiche di progetto, scalabile, sicuro e efficiente.

### 2. Descrizione ad alto livello del sistema
La web app permetterà agli utenti di gestire le proprie attività quotidiane attraverso un'interfaccia intuitiva e reattiva. Gli utenti potranno creare, modificare, eliminare e categorizzare attività, sincronizzarle su dispositivi multipli e ricevere notifiche per le scadenze imminenti.

### 3. Pattern architetturali usati
Il sistema sarà basato su un'architettura **microservizi** per garantire modularità e scalabilità. Verrà adottato il pattern **MVVM** (Model-View-ViewModel) per il frontend e **MVC** (Model-View-Controller) per il backend, facilitando così la manutenzione e la scalabilità del codice.

### 4. Diagramma architetturale globale
```
+-------------+        +------------------+        +--------------+
|   Frontend  | <----> |   Backend/API    | <----> |   Database   |
+-------------+        +------------------+        +--------------+
    (Vue.js)               (Node.js/Express)            (PostgreSQL)
```

### 5. Componenti principali
- **Frontend**: Realizzato con Vue.js per sfruttare il pattern MVVM, garantendo una gestione efficiente della UI dinamica.
- **Backend**: Implementato in Node.js con il framework Express, seguendo il pattern MVC per una chiara separazione delle responsabilità.
- **Database**: PostgreSQL, un sistema di gestione di database relazionale, per la sua affidabilità e capacità di gestire transazioni complesse.

### 6. Relazioni e dipendenze
- Il **Frontend** comunica con il **Backend** tramite API REST per CRUD operazioni. 
- Il **Backend** gestisce la logica di business e interagisce direttamente con il **Database** per la persistenza dei dati.
- Autenticazione gestita tramite OAuth e Google Sign-In per garantire la sicurezza.

### 7. Distribuzione
- **Frontend**: Distribuito come applicazione statica su CDN (Content Delivery Network).
- **Backend e Database**: Hostati su AWS EC2, con bilanciamento del carico per gestire l'alta disponibilità e scalabilità.
- **Backup e Recovery**: Strategie di backup giornaliero e snapshot del database su Amazon S3.

### 8. Tecnologie usate per il deployment
- **Docker**: Containerizzazione del backend e database per facilitare il deployment e la scalabilità.
- **Kubernetes**: Orchestrazione dei container per gestire il deployment e la scalabilità del servizio in modo efficiente.
- **CI/CD**: Pipeline di integrazione continua e distribuzione continua tramite Jenkins.

### 9. Modello dei dati
- Schema del database con tabelle per Utenti, Attività, Categorie, e Notifiche.
- Relazioni chiare tra le entità per ottimizzare le query e mantenere l'integrità dei dati.

### 10. Flusso delle richieste tra componenti
1. **Utente interagisce con il Frontend** (crea/modifica/elimina attività).
2. **Frontend invia richieste API al Backend**.
3. **Backend elabora la richiesta**: autenticazione, logica di business, interazione con il Database.
4. **Risposta inviata al Frontend**.
5. **Frontend aggiorna la UI** in base alla risposta.

### Conclusioni
Questo documento SAD stabilisce una roadmap chiara per lo sviluppo e la distribuzione della web app "ToDo List per la gestione di attività personali". L'architettura proposta garantisce performance, sicurezza e una base solida per future espansioni del software.