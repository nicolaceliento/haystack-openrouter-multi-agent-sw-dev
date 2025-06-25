# Output da: Database Administrator

**Obiettivo:** Progettare e gestire database efficienti e sicuri

**Task:** Implementa i componenti del database per il progetto "{project_name}" selezionando i backlog item relativi al database dal Backlog creato dal Technical Lead. 


---

## Implementazione Database per "ToDo List per la gestione di attività personali"

Questo documento descrive l'implementazione del database PostgreSQL per la web app "ToDo List per la gestione di attività personali", come definito nel backlog tecnico e nel SAD.

### 1. Schema del Database e Script di Creazione

```sql
-- creazione del database
CREATE DATABASE todo_list;

-- connessione al database
\c todo_list;

-- creazione tabella Utenti
CREATE TABLE Users (
    user_id SERIAL PRIMARY KEY,
    username VARCHAR(255) UNIQUE NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255), -- Opzionale, se si usa OAuth potrebbe non servire
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- creazione tabella Categorie
CREATE TABLE Categories (
    category_id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES Users(user_id) ON DELETE CASCADE,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- creazione tabella Tags
CREATE TABLE Tags (
    tag_id SERIAL PRIMARY KEY,
    name VARCHAR(255) UNIQUE NOT NULL
);


-- creazione tabella Tasks
CREATE TABLE Tasks (
    task_id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES Users(user_id) ON DELETE CASCADE,
    category_id INTEGER REFERENCES Categories(category_id) ON DELETE SET NULL, -- permette di eliminare la categoria mantenendo il task
    title VARCHAR(255) NOT NULL,
    description TEXT,
    due_date TIMESTAMP WITH TIME ZONE,
    priority INTEGER CHECK (priority BETWEEN 1 AND 5), -- 1: bassa, 5: alta
    status BOOLEAN DEFAULT FALSE, -- false: incompleto, true: completato
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    completed_at TIMESTAMP WITH TIME ZONE
);

-- creazione tabella Task_Tags (relazione molti-a-molti tra Tasks e Tags)
CREATE TABLE Task_Tags (
    task_id INTEGER REFERENCES Tasks(task_id) ON DELETE CASCADE,
    tag_id INTEGER REFERENCES Tags(tag_id) ON DELETE CASCADE,
    PRIMARY KEY (task_id, tag_id)
);


-- creazione indici
CREATE INDEX idx_tasks_user_id ON Tasks(user_id);
CREATE INDEX idx_tasks_due_date ON Tasks(due_date);
CREATE INDEX idx_tasks_status ON Tasks(status);
CREATE INDEX idx_categories_user_id ON Categories(user_id);
CREATE INDEX idx_task_tags_task_id ON Task_Tags(task_id);
CREATE INDEX idx_task_tags_tag_id ON Task_Tags(tag_id);


```

### 2. Script di Inserimento Dati di Test

```sql
-- Inserimento utente di test
INSERT INTO Users (username, email) VALUES ('testuser', 'test@example.com');

-- Inserimento categorie di test
INSERT INTO Categories (user_id, name) VALUES (1, 'Lavoro');
INSERT INTO Categories (user_id, name) VALUES (1, 'Personale');

-- Inserimento tags di test
INSERT INTO Tags (name) VALUES ('Urgente');
INSERT INTO Tags (name) VALUES ('Importante');

-- Inserimento task di test
INSERT INTO Tasks (user_id, category_id, title, due_date, priority, status) 
VALUES (1, 1, 'Completare report', '2024-07-30 17:00:00', 5, false);

INSERT INTO Tasks (user_id, category_id, title, due_date, priority, status) 
VALUES (1, 2, 'Fare la spesa', '2024-07-28 10:00:00', 3, true);


-- Inserimento relazioni Task-Tag
INSERT INTO Task_Tags (task_id, tag_id) VALUES (1, 1);
INSERT INTO Task_Tags (task_id, tag_id) VALUES (1, 2);

```


### 3. Istruzioni per l'installazione e l'esecuzione

1. **Installare PostgreSQL:** Seguire le istruzioni per il proprio sistema operativo.
2. **Creare il database:** Eseguire lo script di creazione del database.
3. **Connettersi al database:** Usare un client PostgreSQL (psql, pgAdmin) e connettersi al database `todo_list`.
4. **Eseguire gli script SQL:** Copiare e incollare gli script di creazione tabelle e inserimento dati nel client PostgreSQL ed eseguirli.

### 4. Documentazione del Modello ER

```
    Users *--1 Categories
    Users *--1 Tasks
    Categories 1--* Tasks
    Tasks *--* Tags (tramite Task_Tags)

    Users
    - user_id (PK)
    - username
    - email
    - password_hash
    - created_at

    Categories
    - category_id (PK)
    - user_id (FK)
    - name
    - description
    - created_at

    Tasks
    - task_id (PK)
    - user_id (FK)
    - category_id (FK)
    - title
    - description
    - due_date
    - priority
    - status
    - created_at
    - completed_at

    Tags
    - tag_id (PK)
    - name

    Task_Tags
    - task_id (FK)
    - tag_id (FK)

```

### 5. Sicurezza

- **User roles e permessi:** Creare ruoli specifici con permessi limitati per l'applicazione backend. Evitare di usare l'utente postgres per l'applicazione.
- **Encryption at rest:** Abilitare la crittografia del disco dove risiede il database.
- **Backup automation:** Implementare script per backup automatici e testarli regolarmente.


### 6. Query Optimization

Gli indici sono stati creati sulle colonne più frequentemente utilizzate nelle query per migliorare le performance.  Ulteriori ottimizzazioni saranno eseguite durante lo sviluppo in base all'analisi dei piani di esecuzione delle query.

### 7. Backup & Recovery

Si consiglia l'utilizzo di backup incrementali e la configurazione di un sistema di point-in-time recovery.  Un disaster recovery plan dettagliato verrà definito nella fase successiva del progetto.

### 8. Scaling Strategy

Strategie di scaling come partitioning, read replicas e sharding saranno valutate in futuro in base alla crescita dell'applicazione e del carico di lavoro.


Questo documento fornisce una solida base per l'implementazione del database per la "ToDo List per la gestione di attività personali".  Durante lo sviluppo, questo schema potrà essere ulteriormente raffinato e ottimizzato in base alle esigenze del progetto.
