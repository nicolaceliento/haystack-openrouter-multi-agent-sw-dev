# Output da: Frontend Developer

**Obiettivo:** Sviluppare un'interfaccia utente responsiva e moderna

**Task:** Implementa i componenti del frontend per il progetto "{project_name}" selezionando i backlog item relativi al frontend dal Backlog creato dal Technical Lead.  Assicurati che il frontend sia responsivo, moderno e intuitivo.


---

# ToDo List App - Frontend Implementation

## Struttura del Progetto
```
todo-app/
├── public/
│   ├── index.html
│   └── favicon.ico
├── src/
│   ├── assets/
│   │   └── styles/
│   │       ├── main.scss
│   │       └── variables.scss
│   ├── components/
│   │   ├── TaskList.vue
│   │   ├── TaskForm.vue
│   │   ├── TaskItem.vue
│   │   ├── TaskFilter.vue
│   │   ├── TaskSearch.vue
│   │   └── NotificationBell.vue
│   ├── views/
│   │   ├── Home.vue
│   │   ├── Login.vue
│   │   └── Profile.vue
│   ├── store/
│   │   ├── index.js
│   │   └── modules/
│   │       ├── tasks.js
│   │       ├── auth.js
│   │       └── notifications.js
│   ├── router/
│   │   └── index.js
│   ├── services/
│   │   ├── api.js
│   │   └── notifications.js
│   ├── utils/
│   │   └── helpers.js
│   ├── App.vue
│   └── main.js
├── tests/
│   └── unit/
│       ├── TaskList.spec.js
│       └── TaskForm.spec.js
├── package.json
└── README.md
```

## Installazione e Setup

```bash
# Clona il repository
git clone https://github.com/yourusername/todo-app.git

# Entra nella directory
cd todo-app

# Installa le dipendenze
npm install

# Avvia il server di sviluppo
npm run serve

# Build per produzione
npm run build

# Esegui i test
npm run test:unit
```

## Componenti Principali

### App.vue
```vue
<template>
  <div id="app">
    <nav class="navbar">
      <router-link to="/">Home</router-link>
      <router-link to="/login" v-if="!isAuthenticated">Login</router-link>
      <router-link to="/profile" v-if="isAuthenticated">Profile</router-link>
      <notification-bell v-if="isAuthenticated"/>
    </nav>
    <router-view></router-view>
  </div>
</template>

<script>
import { mapGetters } from 'vuex';
import NotificationBell from '@/components/NotificationBell.vue';

export default {
  name: 'App',
  components: {
    NotificationBell
  },
  computed: {
    ...mapGetters(['isAuthenticated'])
  }
}
</script>
```

### TaskList.vue
```vue
<template>
  <div class="task-list">
    <task-filter @filter-change="handleFilterChange"/>
    <task-search @search="handleSearch"/>
    
    <div class="tasks-container">
      <task-item
        v-for="task in filteredTasks"
        :key="task.id"
        :task="task"
        @update="updateTask"
        @delete="deleteTask"
        @toggle-complete="toggleComplete"
      />
    </div>
    
    <task-form @submit="createTask"/>
  </div>
</template>

<script>
import { mapActions, mapGetters } from 'vuex';
import TaskItem from './TaskItem.vue';
import TaskFilter from './TaskFilter.vue';
import TaskSearch from './TaskSearch.vue';
import TaskForm from './TaskForm.vue';

export default {
  name: 'TaskList',
  components: {
    TaskItem,
    TaskFilter,
    TaskSearch,
    TaskForm
  },
  computed: {
    ...mapGetters(['filteredTasks'])
  },
  methods: {
    ...mapActions(['fetchTasks', 'createTask', 'updateTask', 'deleteTask']),
    handleFilterChange(filters) {
      this.$store.commit('SET_FILTERS', filters);
    },
    handleSearch(query) {
      this.$store.commit('SET_SEARCH_QUERY', query);
    },
    toggleComplete(taskId) {
      const task = this.filteredTasks.find(t => t.id === taskId);
      this.updateTask({
        ...task,
        completed: !task.completed
      });
    }
  },
  created() {
    this.fetchTasks();
  }
}
</script>
```

### Store Configuration (store/index.js)
```javascript
import Vue from 'vue';
import Vuex from 'vuex';
import tasks from './modules/tasks';
import auth from './modules/auth';
import notifications from './modules/notifications';

Vue.use(Vuex);

export default new Vuex.Store({
  modules: {
    tasks,
    auth,
    notifications
  }
});
```

### API Service (services/api.js)
```javascript
import axios from 'axios';

const api = axios.create({
  baseURL: process.env.VUE_APP_API_URL,
  timeout: 5000
});

// Interceptor per aggiungere token di autenticazione
api.interceptors.request.use(config => {
  const token = localStorage.getItem('token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

export default {
  // Task APIs
  getTasks: () => api.get('/tasks'),
  createTask: (task) => api.post('/tasks', task),
  updateTask: (id, task) => api.put(`/tasks/${id}`, task),
  deleteTask: (id) => api.delete(`/tasks/${id}`),
  
  // Auth APIs
  login: (credentials) => api.post('/auth/login', credentials),
  register: (userData) => api.post('/auth/register', userData),
  
  // Notification APIs
  getNotifications: () => api.get('/notifications')
};
```

## Test Unitari

### TaskList.spec.js
```javascript
import { shallowMount, createLocalVue } from '@vue/test-utils';
import Vuex from 'vuex';
import TaskList from '@/components/TaskList.vue';

const localVue = createLocalVue();
localVue.use(Vuex);

describe('TaskList.vue', () => {
  let actions;
  let store;
  let wrapper;

  beforeEach(() => {
    actions = {
      fetchTasks: jest.fn(),
      createTask: jest.fn(),
      updateTask: jest.fn(),
      deleteTask: jest.fn()
    };

    store = new Vuex.Store({
      modules: {
        tasks: {
          state: {
            tasks: []
          },
          actions,
          getters: {
            filteredTasks: () => []
          }
        }
      }
    });

    wrapper = shallowMount(TaskList, {
      store,
      localVue
    });
  });

  it('fetches tasks on created', () => {
    expect(actions.fetchTasks).toHaveBeenCalled();
  });

  // Altri test...
});
```

## Stili (assets/styles/main.scss)
```scss
@import 'variables';

.task-list {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;

  .tasks-container {
    margin-top: 20px;
  }
}

.task-item {
  background: $card-bg;
  border-radius: $border-radius;
  padding: 15px;
  margin-bottom: 10px;
  box-shadow: $box-shadow;
  
  &.completed {
    opacity: 0.7;
  }
}

// Altri stili...
```

## Note Implementative
- Utilizzo di Vue.js 2.x con Composition API per migliore organizzazione del codice
- Vuex per la gestione dello stato dell'applicazione
- Vue Router per la navigazione
- Axios per le chiamate API
- SCSS per gli stili con design system consistente
- Jest per i test unitari
- Supporto per PWA e responsive design
- Implementazione di lazy loading per ottimizzare le performance

Per ulteriori dettagli sull'implementazione, consultare la documentazione inline nei singoli file.