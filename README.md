# 🧵 Threads and Semaphores Visualizer

![HTML](https://img.shields.io/badge/HTML-E34F26?logo=html5&logoColor=white)
![CSS](https://img.shields.io/badge/CSS-1572B6?logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?logo=javascript&logoColor=black)

An interactive, step-by-step animation of POSIX threads and semaphores, followed by a beginner's guide with C examples.

**🌐 [Live demo](https://adelmerad.github.io/thread-animation/)**

## ✨ Features

- **Threads tab:** follow `pthread_create()`, `pthread_join()` and `pthread_exit()` on two timelines, with the shared heap and separate stacks
- **Semaphores tab:** watch two threads compete for a critical section, with the semaphore value, blocked states and unblocking
- Play, pause, step forward and restart
- A single HTML file with no dependencies: open `index.html` in a browser

---

# 📖 Guide

## What Is a Thread?

A **thread** is like a small program running inside a main program.

### Simple analogy

Picture a kitchen:
- **Process** = the whole kitchen
- **Main thread** = the head chef
- **Created thread** = the chef's assistant

Both can work **at the same time** (in parallel) and share the same utensils (memory).

---

## 🔧 The 3 Main Functions

### 1. `pthread_create()` - Create a thread
```c
pthread_t thread;
pthread_create(&thread, NULL, function, NULL);
```
**What it does:** creates a new thread that runs a function

**Example:** the chef tells the assistant "Go wash the vegetables"

---

### 2. `pthread_join()` - Wait for a thread
```c
pthread_join(thread, NULL);
```
**What it does:** waits for the thread to finish before continuing

**Example:** the chef waits for the assistant to finish washing the vegetables

---

### 3. `pthread_exit()` - End a thread
```c
pthread_exit(NULL);
```
**What it does:** ends the thread cleanly

**Example:** the assistant says "I'm done!"

---

## 🚦 What Is a Semaphore?

A **semaphore** is like a traffic light that controls access to a shared resource.

### Simple analogy

Picture a **public restroom** with a single stall:
- **Semaphore = 1** → free (green light) ✅
- **Semaphore = 0** → occupied (red light) ❌
- The person going in locks the door → `sem_wait()`
- The person coming out unlocks it → `sem_post()`

---

## 🔧 The 4 Semaphore Functions

### 1. `sem_init()` - Initialize
```c
sem_t semaphore;
sem_init(&semaphore, 0, 1);  // 1 = available
```
**What it does:** creates the semaphore with an initial value

---

### 2. `sem_wait()` - Take (P operation)
```c
sem_wait(&semaphore);  // Decrements by 1
```
**What it does:**
- If semaphore > 0 → decrements and continues
- If semaphore = 0 → **BLOCKS** and waits

**Example:** trying to enter the restroom
- If it's free → I go in and lock the door
- If it's occupied → I wait outside

---

### 3. `sem_post()` - Release (V operation)
```c
sem_post(&semaphore);  // Increments by 1
```
**What it does:** releases the resource and wakes a waiting thread

**Example:** I leave the restroom and unlock the door

---

### 4. `sem_destroy()` - Destroy
```c
sem_destroy(&semaphore);
```
**What it does:** frees the semaphore's resources

---

## 💡 Full Example with Threads

```c
#include <pthread.h>
#include <stdio.h>

void* say_hello(void* arg) {
    printf("Hello from the thread!\n");
    return NULL;
}

int main() {
    pthread_t thread;

    // 1. Create the thread
    pthread_create(&thread, NULL, say_hello, NULL);

    printf("Main thread continues...\n");

    // 2. Wait for the thread
    pthread_join(thread, NULL);

    printf("All done!\n");
    return 0;
}
```

**Compile and run:**
```bash
gcc example.c -o example -pthread
./example
```

---

## 💡 Full Example with a Semaphore

```c
#include <pthread.h>
#include <semaphore.h>
#include <stdio.h>

sem_t semaphore;
int counter = 0;

void* increment(void* arg) {
    sem_wait(&semaphore);      // Lock

    // Critical section
    counter++;
    printf("Counter = %d\n", counter);

    sem_post(&semaphore);      // Unlock
    return NULL;
}

int main() {
    pthread_t t1, t2;

    // Initialize the semaphore to 1
    sem_init(&semaphore, 0, 1);

    // Create 2 threads
    pthread_create(&t1, NULL, increment, NULL);
    pthread_create(&t2, NULL, increment, NULL);

    // Wait for the threads
    pthread_join(t1, NULL);
    pthread_join(t2, NULL);

    // Destroy the semaphore
    sem_destroy(&semaphore);

    return 0;
}
```

---

## 📊 Key Differences

| Concept | Threads | Semaphores |
|---------|---------|------------|
| **Purpose** | Run code in parallel | Synchronize access to resources |
| **Problem solved** | Doing several tasks at once | Avoiding access conflicts |
| **Library** | `<pthread.h>` | `<semaphore.h>` |
| **Analogy** | Several cooks | A door lock |

---

## ⚠️ Important Points

### For threads
- ✅ Always use `pthread_join()` to avoid memory leaks
- ✅ Threads share memory (watch out for conflicts!)
- ✅ Compile with `-pthread`

### For semaphores
- ✅ Always call `sem_wait()` before accessing the resource
- ✅ Always call `sem_post()` when you're done
- ✅ Initialize with `sem_init()` BEFORE use
- ✅ Destroy with `sem_destroy()` at the end

---

## 🎯 When to Use What

### Use **threads** to
- Run several tasks at the same time
- Improve performance (parallel computation)
- Handle several clients at once (servers)

### Use **semaphores** to
- Protect a shared variable
- Limit access to a resource (e.g. 5 connections at most)
- Synchronize threads

---

## ❓ FAQ

**Q: Why won't my code compile on Windows?**  
A: POSIX threads work on Linux and Unix. On Windows, use WSL.

**Q: What happens if I forget `pthread_join()`?**  
A: The program can exit before the thread finishes, which causes bugs.

**Q: Can I have several `sem_wait()` calls?**  
A: Yes, but watch out for **deadlocks**!

**Q: What's the difference between a process and a thread?**  
A: A process has its own memory. Threads share the memory of their process.

---

## ✨ Summary in 3 Lines

1. **Threads** = run several things in parallel
2. **Semaphores** = protect shared resources with a counter
3. **Always:** create → wait/post → join → destroy

---

## 🔗 Resources

- [pthreads documentation](https://man7.org/linux/man-pages/man7/pthreads.7.html)
- [Semaphores documentation](https://man7.org/linux/man-pages/man7/sem_overview.7.html)

## 👤 Author

**Adel Merad** — [GitHub](https://github.com/adelmerad)
