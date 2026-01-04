# 🧵 Guide Simple : Threads et Sémaphores

## 📖 Qu'est-ce qu'un Thread ?

Un **thread** (fil d'exécution) est comme un mini-programme qui s'exécute à l'intérieur d'un programme principal.

### Analogie simple :
Imaginez une cuisine :
- **Processus** = La cuisine entière
- **Thread principal** = Le chef cuisinier
- **Thread créé** = L'assistant du chef

Les deux peuvent travailler **en même temps** (parallèle) et partagent les mêmes ustensiles (mémoire).

---

## 🔧 Les 3 Fonctions Principales

### 1. `pthread_create()` - Créer un thread
```c
pthread_t thread;
pthread_create(&thread, NULL, fonction, NULL);
```
**Que fait-elle ?** Crée un nouveau thread qui exécute une fonction

**Exemple :** Le chef dit à l'assistant "Va laver les légumes"

---

### 2. `pthread_join()` - Attendre un thread
```c
pthread_join(thread, NULL);
```
**Que fait-elle ?** Attend que le thread se termine avant de continuer

**Exemple :** Le chef attend que l'assistant finisse de laver les légumes

---

### 3. `pthread_exit()` - Terminer un thread
```c
pthread_exit(NULL);
```
**Que fait-elle ?** Termine le thread proprement

**Exemple :** L'assistant dit "J'ai fini !"
