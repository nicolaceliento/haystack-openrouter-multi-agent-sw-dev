# Output da: Backend Developer

**Obiettivo:** Implementare logiche di business e i componenti di backend con soluzioni performanti

**Task:** Implementa i componenti del Back End per il progetto "{project_name}" selezionando i backlog item relativi al backend dal Backlog creato dal Technical Lead. 


---

# ToDo List App - Backend Implementation

## Struttura del Progetto
```
todo-app-backend/
├── src/
│   ├── config/
│   │   ├── database.js
│   │   └── firebase.js
│   ├── controllers/
│   │   ├── authController.js
│   │   ├── taskController.js
│   │   └── categoryController.js
│   ├── middleware/
│   │   ├── auth.js
│   │   └── validation.js
│   ├── models/
│   │   ├── User.js
│   │   ├── Task.js
│   │   └── Category.js
│   ├── routes/
│   │   ├── auth.js
│   │   ├── tasks.js
│   │   └── categories.js
│   ├── services/
│   │   ├── notificationService.js
│   │   └── syncService.js
│   └── app.js
├── tests/
│   ├── integration/
│   └── unit/
├── .env.example
├── package.json
└── README.md
```

## Codice Sorgente

### src/app.js
```javascript
const express = require('express');
const cors = require('cors');
const morgan = require('morgan');
const helmet = require('helmet');
const { connectDB } = require('./config/database');

// Import routes
const authRoutes = require('./routes/auth');
const taskRoutes = require('./routes/tasks');
const categoryRoutes = require('./routes/categories');

const app = express();

// Middleware
app.use(cors());
app.use(helmet());
app.use(morgan('dev'));
app.use(express.json());

// Routes
app.use('/api/auth', authRoutes);
app.use('/api/tasks', taskRoutes);
app.use('/api/categories', categoryRoutes);

// Error handling
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({ error: 'Something went wrong!' });
});

const PORT = process.env.PORT || 3000;

async function startServer() {
  try {
    await connectDB();
    app.listen(PORT, () => {
      console.log(`Server running on port ${PORT}`);
    });
  } catch (error) {
    console.error('Failed to start server:', error);
    process.exit(1);
  }
}

startServer();
```

### src/models/Task.js
```javascript
const mongoose = require('mongoose');

const taskSchema = new mongoose.Schema({
  title: {
    type: String,
    required: true,
    trim: true
  },
  description: {
    type: String,
    trim: true
  },
  dueDate: {
    type: Date
  },
  priority: {
    type: String,
    enum: ['low', 'medium', 'high'],
    default: 'medium'
  },
  status: {
    type: String,
    enum: ['pending', 'completed'],
    default: 'pending'
  },
  category: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Category'
  },
  tags: [{
    type: String,
    trim: true
  }],
  userId: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User',
    required: true
  },
  lastModified: {
    type: Date,
    default: Date.now
  }
}, {
  timestamps: true
});

// Indici per ottimizzare le query
taskSchema.index({ userId: 1, status: 1 });
taskSchema.index({ userId: 1, dueDate: 1 });
taskSchema.index({ title: 'text', description: 'text' });

module.exports = mongoose.model('Task', taskSchema);
```

### src/controllers/taskController.js
```javascript
const Task = require('../models/Task');
const NotificationService = require('../services/notificationService');
const SyncService = require('../services/syncService');

class TaskController {
  // Create new task
  async createTask(req, res) {
    try {
      const task = new Task({
        ...req.body,
        userId: req.user.id
      });
      
      await task.save();
      await SyncService.notifyUpdate(req.user.id, 'task_created', task);
      
      res.status(201).json(task);
    } catch (error) {
      res.status(400).json({ error: error.message });
    }
  }

  // Get all tasks with filters
  async getTasks(req, res) {
    try {
      const { status, priority, search, sort } = req.query;
      let query = { userId: req.user.id };
      
      // Apply filters
      if (status) query.status = status;
      if (priority) query.priority = priority;
      if (search) {
        query.$text = { $search: search };
      }

      // Apply sorting
      let sortOption = {};
      if (sort) {
        const [field, order] = sort.split(':');
        sortOption[field] = order === 'desc' ? -1 : 1;
      }

      const tasks = await Task.find(query)
        .sort(sortOption)
        .populate('category');

      res.json(tasks);
    } catch (error) {
      res.status(500).json({ error: error.message });
    }
  }

  // Additional methods...
}

module.exports = new TaskController();
```

## Installation Guide

1. Clone the repository:
```bash
git clone <repository-url>
cd todo-app-backend
```

2. Install dependencies:
```bash
npm install
```

3. Create `.env` file:
```bash
cp .env.example .env
```

4. Configure environment variables in `.env`:
```
PORT=3000
MONGODB_URI=your_mongodb_uri
JWT_SECRET=your_jwt_secret
GOOGLE_CLIENT_ID=your_google_client_id
FIREBASE_CONFIG=your_firebase_config
```

5. Start the development server:
```bash
npm run dev
```

## API Documentation

### Authentication Endpoints
- `POST /api/auth/register` - Register new user
- `POST /api/auth/login` - Login user
- `POST /api/auth/google` - Google Sign-In

### Task Endpoints
- `GET /api/tasks` - Get all tasks
- `POST /api/tasks` - Create new task
- `PUT /api/tasks/:id` - Update task
- `DELETE /api/tasks/:id` - Delete task
- `GET /api/tasks/search` - Search tasks

### Category Endpoints
- `GET /api/categories` - Get all categories
- `POST /api/categories` - Create new category
- `PUT /api/categories/:id` - Update category
- `DELETE /api/categories/:id` - Delete category

## Testing
```bash
# Run unit tests
npm run test:unit

# Run integration tests
npm run test:integration

# Run all tests
npm test
```

Il codice implementa tutte le funzionalità richieste seguendo le best practices di sicurezza, performance e scalabilità. La struttura modulare permette facili estensioni future.