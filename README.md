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

---

## 🚦 Qu'est-ce qu'un Sémaphore ?

Un **sémaphore** est comme un feu de signalisation pour contrôler l'accès à une ressource partagée.

### Analogie simple :
Imaginez des **toilettes publiques** avec une seule cabine :
- **Sémaphore = 1** → Toilettes libres (feu vert) ✅
- **Sémaphore = 0** → Toilettes occupées (feu rouge) ❌
- La personne qui entre met le verrou → `sem_wait()`
- La personne qui sort déverrouille → `sem_post()`

---

## 🔧 Les 4 Fonctions de Sémaphore

### 1. `sem_init()` - Initialiser
```c
sem_t semaphore;
sem_init(&semaphore, 0, 1);  // 1 = disponible
```
**Que fait-elle ?** Crée le sémaphore avec une valeur initiale

---

### 2. `sem_wait()` - Prendre (opération P)
```c
sem_wait(&semaphore);  // Décrémente de 1
```
**Que fait-elle ?**
- Si sémaphore > 0 → Décrémente et continue
- Si sémaphore = 0 → **BLOQUE** et attend

**Exemple :** Essayer d'entrer dans les toilettes
- Si libre → J'entre et je verrouille
- Si occupé → J'attends dehors

---

### 3. `sem_post()` - Libérer (opération V)
```c
sem_post(&semaphore);  // Incrémente de 1
```
**Que fait-elle ?** Libère la ressource et réveille un thread en attente

**Exemple :** Je sors des toilettes et je déverrouille

---

### 4. `sem_destroy()` - Détruire
```c
sem_destroy(&semaphore);
```
**Que fait-elle ?** Libère les ressources du sémaphore

---

## 💡 Exemple Complet avec Threads

```c
#include <pthread.h>
#include <stdio.h>

void* dire_bonjour(void* arg) {
    printf("Bonjour depuis le thread !\n");
    return NULL;
}

int main() {
    pthread_t thread;
    
    // 1. Créer le thread
    pthread_create(&thread, NULL, dire_bonjour, NULL);
    
    printf("Thread principal continue...\n");
    
    // 2. Attendre le thread
    pthread_join(thread, NULL);
    
    printf("Tout est terminé !\n");
    return 0;
}
```

**Compilation :**
```bash
gcc exemple.c -o exemple -pthread
./exemple
```

---

## 💡 Exemple Complet avec Sémaphore

```c
#include <pthread.h>
#include <semaphore.h>
#include <stdio.h>

sem_t semaphore;
int compteur = 0;

void* incrementer(void* arg) {
    sem_wait(&semaphore);      // Verrouiller
    
    // Section critique
    compteur++;
    printf("Compteur = %d\n", compteur);
    
    sem_post(&semaphore);      // Déverrouiller
    return NULL;
}

int main() {
    pthread_t t1, t2;
    
    // Initialiser le sémaphore à 1
    sem_init(&semaphore, 0, 1);
    
    // Créer 2 threads
    pthread_create(&t1, NULL, incrementer, NULL);
    pthread_create(&t2, NULL, incrementer, NULL);
    
    // Attendre les threads
    pthread_join(t1, NULL);
    pthread_join(t2, NULL);
    
    // Détruire le sémaphore
    sem_destroy(&semaphore);
    
    return 0;
}
```

---

## 📊 Différences Clés

| Concept | Threads | Sémaphores |
|---------|---------|------------|
| **But** | Exécuter du code en parallèle | Synchroniser l'accès aux ressources |
| **Problème résolu** | Faire plusieurs tâches en même temps | Éviter les conflits d'accès |
| **Bibliothèque** | `<pthread.h>` | `<semaphore.h>` |
| **Analogie** | Plusieurs cuisiniers | Verrou de porte |

---

## ⚠️ Points Importants

### Pour les Threads :
- ✅ Toujours utiliser `pthread_join()` pour éviter les fuites mémoire
- ✅ Les threads partagent la mémoire (attention aux conflits !)
- ✅ Compiler avec `-pthread`

### Pour les Sémaphores :
- ✅ Toujours `sem_wait()` avant d'accéder à la ressource
- ✅ Toujours `sem_post()` après avoir fini
- ✅ Initialiser avec `sem_init()` AVANT d'utiliser
- ✅ Détruire avec `sem_destroy()` à la fin

---

## 🎯 Quand Utiliser Quoi ?

### Utilisez les **Threads** pour :
- Exécuter plusieurs tâches simultanément
- Améliorer les performances (calculs parallèles)
- Gérer plusieurs clients en même temps (serveur)

### Utilisez les **Sémaphores** pour :
- Protéger une variable partagée
- Limiter l'accès à une ressource (ex: max 5 connexions)
- Synchroniser des threads

---

## 🔗 Ressources Utiles

- [Documentation pthread](https://man7.org/linux/man-pages/man7/pthreads.7.html)
- [Documentation sémaphores](https://man7.org/linux/man-pages/man7/sem_overview.7.html)
- Compilez toujours avec : `gcc fichier.c -o fichier -pthread`

---

## ❓ Questions Fréquentes

**Q: Pourquoi mon code ne compile pas sous Windows ?**  
R: Les threads POSIX fonctionnent sur Linux/Unix. Sous Windows, utilisez WSL.

**Q: Que se passe-t-il si j'oublie pthread_join() ?**  
R: Le programme peut se terminer avant le thread, causant des bugs.

**Q: Puis-je avoir plusieurs sem_wait() ?**  
R: Oui, mais attention aux **deadlocks** (blocages) !

**Q: Quelle est la différence entre processus et thread ?**  
R: Un processus a sa propre mémoire. Les threads partagent la mémoire du processus.

---

## ✨ Résumé en 3 Lignes

1. **Threads** = Exécuter plusieurs choses en parallèle
2. **Sémaphores** = Protéger les ressources partagées avec un compteur
3. **Toujours** : create → wait/post → join → destroy

---
