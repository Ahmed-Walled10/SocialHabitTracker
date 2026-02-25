<div align="center">

# 🌟 Social Habit Tracker

> A full-featured social habit tracking platform built with **ASP.NET 9** and **Clean Architecture** — track your habits, connect with friends, compete in groups, and level up your consistency.

[![ASP.NET 9](https://img.shields.io/badge/ASP.NET-9.0-512BD4?style=for-the-badge&logo=dotnet)](https://dotnet.microsoft.com/)
[![Clean Architecture](https://img.shields.io/badge/Architecture-Clean-blue?style=for-the-badge)]()
[![Status](https://img.shields.io/badge/Status-In%20Development-yellow?style=for-the-badge)]()

</div>

---

## 📖 Table of Contents

- [Overview](#-overview)
- [Tech Stack](#-tech-stack)
- [Architecture](#-architecture)
- [Business Requirements](#-business-requirements)
- [Database Design Notes](#-database-design-notes)
- [Development Phases](#-development-phases)
- [Project Structure](#-project-structure)
- [Getting Started](#-getting-started)

---

## 🧭 Overview

**Social Habit Tracker** is a web application that helps users build and maintain habits through social accountability. Users can track daily, weekly, or custom habits, connect with friends, join groups, compete on leaderboards, and earn achievements — all while keeping streaks alive with timezone-aware calculations.

---

## 🛠 Tech Stack

| Layer | Technology |
|---|---|
| **Backend** | ASP.NET 9 (Web API) |
| **Architecture** | Clean Architecture |
| **ORM** | Entity Framework Core |
| **Auth** | ASP.NET Identity + JWT |
| **Database** | SQL Server / PostgreSQL |
| **Background Jobs** | Hangfire / Quartz.NET |
| **Real-time** | SignalR (notifications) |
| **Testing** | xUnit + Moq |

---

## 🏛 Architecture

This project follows **Clean Architecture** (also known as Onion Architecture), separating concerns into four distinct layers:

```
src/
├── Domain/          # Entities, value objects, domain events, interfaces
├── Application/     # Use cases, DTOs, commands/queries (CQRS via MediatR)
├── Infrastructure/  # EF Core, repositories, external services, email, jobs
└── WebAPI/          # Controllers, middleware, DI configuration, filters
```

**Key Principles:**
- The **Domain** layer has zero external dependencies
- The **Application** layer depends only on Domain
- The **Infrastructure** and **WebAPI** layers implement the interfaces defined in Domain/Application
- CQRS pattern is used via **MediatR** for clean command/query separation

---

## 📋 Business Requirements

### 1️⃣ User Management

- Users can register, log in, and manage their profiles
- Each user sets their **timezone** (critical for streak accuracy)
- Users can **add/remove friends** — friendship is always **mutual**
- Users can **block** other users (optional)
- Friend requests are **directional** (A sends to B) but friendship is **symmetric**

---

### 2️⃣ Habit Management

- Users can **create, edit, delete, and archive** habits
- Each habit supports configurable frequency:
  - **Daily** — must complete once per day
  - **Weekly** — must complete N times per week
  - **Custom days** — specific days of the week (e.g., Mon/Wed/Fri)
- Users can set a **reminder time** per habit
- Habits are assigned to a **category**
- Habits can be marked with **privacy settings**: Private / Friends-Only / Public *(optional)*

> ⚠️ **Rules:** A habit belongs to exactly one user. Archived habits cannot be completed. Users cannot exceed the allowed completions for a frequency period.

---

### 3️⃣ Habit Completion Tracking

- Every completion is stored as a **separate record** with a timestamp
- Undo completion is supported *(optional)*
- Each completion is linked to both a **habit** and a **user**
- Completions directly affect **streak calculations**

---

### 4️⃣ Streak System

- System calculates:
  - ✅ **Current streak**
  - 🏆 **Longest streak**
  - ❌ **Streak breaks** when a required completion is missed
- Streak logic is **timezone-aware** — determined per user's configured timezone
- Streak is evaluated against the habit's configured frequency

---

### 5️⃣ Social / Friends

- Users can **send, accept, and reject** friend requests
- Friend requests carry a status: `Pending`, `Accepted`, or `Rejected`
- Users can **remove friends**
- Users can **view friends' habit progress** (subject to privacy settings)

---

### 6️⃣ Groups

- Users can **create, join, and leave** groups
- Group members can be **invited** by existing members
- Each member has a **role**: `Owner`, `Admin`, or `Member`
- A user can belong to multiple groups; a group can contain multiple users

---

### 7️⃣ Group Habit Sharing

- Users can **share selected habits** with a group
- Shared habits remain **owned by the original user** — sharing ≠ transfer
- Groups have a **leaderboard** based on completion counts
- Group **progress statistics** are tracked and displayed

---

### 8️⃣ Achievements & Badges

- Achievements are **automatically awarded** by the system
- Each achievement stores its **unlock timestamp**
- Supported achievement types include:
  - 🥇 First completion
  - 🔥 7-day streak
  - 💪 30-day streak
  - 👑 Group leaderboard winner
  - *(and more — extensible by design)*
- Design decision: achievements can be configured as **one-time** or **repeatable**

---

### 9️⃣ Notifications & Reminders

- Users receive notifications for:
  - ⏰ Upcoming habit reminders
  - 🏅 Achievement unlocked
  - 👥 Friend activity updates
  - 📨 Group invitations
- Users can **opt out** of any notification category

---

### 🔥 Optional / Advanced Features

| Feature | Description |
|---|---|
| **Privacy Controls** | Per-habit visibility: Private / Friends-Only / Public |
| **Analytics** | Completion rate, consistency score, weekly report |
| **Gamification** | Points per completion, levels, global leaderboard |

---

## 🗃 Database Design Notes

The following key design decisions drive the ERD and schema:

- A **Habit** has many **Completions**; a Completion belongs to exactly one Habit
- A **HabitFrequency** is a configurable rule (not just a text field) — stored with type + parameters
- **Friendship** is symmetric but the **FriendRequest** is directional (sender → receiver)
- A **GroupMember** is an associative table that carries a **Role** attribute
- **SharedHabit** is an associative table linking a Habit to a Group — ownership is preserved
- **Achievements** can be designed as earn-once or earn-multiple (flag on AchievementDefinition)
- **Streak** data can be stored as a computed/cached entity, recalculated on completion events

---

## 🚀 Development Phases

### ✅ Phase 1 — Foundation & Auth
> *Goal: Deployable API skeleton with user registration and login*

- [ ] Initialize solution with Clean Architecture folder structure
- [ ] Configure EF Core with initial migrations
- [ ] Implement ASP.NET Identity for user registration/login
- [ ] Add JWT authentication + refresh tokens
- [ ] User profile management (edit, timezone setting)
- [ ] Global error handling middleware
- [ ] Logging setup (Serilog)

---

### ✅ Phase 2 — Core Habit Features
> *Goal: Fully working solo habit tracking*

- [ ] Habit entity design (frequency config, category, reminder)
- [ ] CRUD for habits (create, edit, delete, archive)
- [ ] Habit completion recording
- [ ] Undo completion support *(optional)*
- [ ] Completion validation against frequency rules
- [ ] Streak calculation engine (timezone-aware)
- [ ] Current streak + longest streak tracking

---

### ✅ Phase 3 — Social Layer
> *Goal: Users can connect and see each other's progress*

- [ ] Friend request system (send / accept / reject / remove)
- [ ] Friends list with activity view
- [ ] Privacy settings on habits (private / friends-only / public)
- [ ] Block user functionality *(optional)*

---

### ✅ Phase 4 — Groups
> *Goal: Group creation and habit sharing*

- [ ] Group CRUD (create, edit, delete)
- [ ] Join / leave group
- [ ] Group member roles (owner / admin / member)
- [ ] Invite friends to group
- [ ] Habit sharing to groups
- [ ] Group leaderboard (by completions)
- [ ] Group progress statistics

---

### ✅ Phase 5 — Achievements System
> *Goal: Automatic badge awarding*

- [ ] Achievement definition model (type, criteria, one-time flag)
- [ ] Achievement evaluation engine (event-driven via MediatR domain events)
- [ ] Achievement unlock storage with timestamp
- [ ] Achievement list / profile display

---

### ✅ Phase 6 — Notifications
> *Goal: Real-time and scheduled alerts*

- [ ] Notification preference model per user
- [ ] Scheduled habit reminders (background job via Hangfire/Quartz)
- [ ] Achievement unlock notifications
- [ ] Friend activity notifications
- [ ] Group invite notifications
- [ ] SignalR for real-time push *(optional)*

---

### ✅ Phase 7 — Analytics & Gamification *(Optional)*
> *Goal: Insights and engagement layer*

- [ ] Completion rate calculation
- [ ] Consistency score
- [ ] Weekly habit report generation
- [ ] Points per completion
- [ ] User levels
- [ ] Global leaderboard

---

### ✅ Phase 8 — Polish & Production Readiness
> *Goal: Stable, tested, deployable application*

- [ ] Unit tests for domain logic (streaks, achievements, frequency validation)
- [ ] Integration tests for API endpoints
- [ ] API versioning
- [ ] Rate limiting
- [ ] Swagger / OpenAPI documentation
- [ ] Docker support
- [ ] CI/CD pipeline setup

---

## 📁 Project Structure

```
SocialHabitTracker/
│
├── src/
│   ├── SocialHabitTracker.Domain/
│   │   ├── Entities/
│   │   │   ├── User.cs
│   │   │   ├── Habit.cs
│   │   │   ├── HabitCompletion.cs
│   │   │   ├── Streak.cs
│   │   │   ├── FriendRequest.cs
│   │   │   ├── Group.cs
│   │   │   ├── GroupMember.cs
│   │   │   ├── SharedHabit.cs
│   │   │   ├── Achievement.cs
│   │   │   └── Notification.cs
│   │   ├── Enums/
│   │   ├── ValueObjects/
│   │   ├── Events/
│   │   └── Interfaces/
│   │
│   ├── SocialHabitTracker.Application/
│   │   ├── Features/
│   │   │   ├── Habits/
│   │   │   ├── Users/
│   │   │   ├── Friends/
│   │   │   ├── Groups/
│   │   │   ├── Achievements/
│   │   │   └── Notifications/
│   │   ├── DTOs/
│   │   ├── Interfaces/
│   │   └── Common/
│   │
│   ├── SocialHabitTracker.Infrastructure/
│   │   ├── Persistence/
│   │   │   ├── AppDbContext.cs
│   │   │   ├── Configurations/
│   │   │   └── Migrations/
│   │   ├── Repositories/
│   │   ├── Services/
│   │   │   ├── StreakService.cs
│   │   │   ├── AchievementService.cs
│   │   │   └── NotificationService.cs
│   │   └── Jobs/
│   │
│   └── SocialHabitTracker.WebAPI/
│       ├── Controllers/
│       ├── Middleware/
│       ├── Filters/
│       └── Program.cs
│
└── tests/
    ├── SocialHabitTracker.Domain.Tests/
    ├── SocialHabitTracker.Application.Tests/
    └── SocialHabitTracker.WebAPI.Tests/
```

---

## ⚡ Getting Started

```bash
# Clone the repository
git clone https://github.com/yourusername/social-habit-tracker.git
cd social-habit-tracker

# Restore dependencies
dotnet restore

# Update database
dotnet ef database update --project src/SocialHabitTracker.Infrastructure

# Run the API
dotnet run --project src/SocialHabitTracker.WebAPI
```

> API will be available at `https://localhost:5001` with Swagger UI at `/swagger`

---

<div align="center">

Made with ❤️ using ASP.NET 9 · Clean Architecture · EF Core

</div>
