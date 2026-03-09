# Questions / Réponses — Spring MVC
### Support d'entretien — à parcourir directement face au stagiaire

> **Mode d'emploi :** Poser la question à voix haute, laisser le stagiaire répondre, puis vérifier ci-dessous.  
> ✅ = réponse correcte attendue · ⚠️ = piège courant · 🚨 = red flag éliminatoire

---

## 1. DispatcherServlet

---

**Q1 — Qu'est-ce que le `DispatcherServlet` ?**

✅ C'est le **Front Controller** de Spring MVC.  
Il est déclaré dans `web.xml` et intercepte **toutes** les requêtes HTTP destinées à l'application.  
Il délègue ensuite le traitement au bon `Controller` en passant par plusieurs composants internes (HandlerMapping, HandlerAdapter, ViewResolver…).

```xml
<!-- web.xml -->
<servlet>
    <servlet-name>dispatcher</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>dispatcher</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

⚠️ **Piège :** Le `DispatcherServlet` est techniquement un servlet (il étend `HttpServlet`), mais son rôle va bien au-delà d'un servlet ordinaire — c'est l'orchestrateur central de tout le framework Spring MVC.

---

**Q2 — Décris le cycle complet d'une requête HTTP dans Spring MVC, de l'arrivée jusqu'à l'affichage de la vue.**

✅ Étapes dans l'ordre :

1. Le navigateur envoie une requête HTTP.
2. Le `DispatcherServlet` la reçoit (point d'entrée unique).
3. Il interroge le **HandlerMapping** → identifie quelle méthode de quel Controller doit traiter la requête.
4. Il invoque le **HandlerAdapter** → appelle la méthode du Controller.
5. Le Controller exécute la logique métier (via le Service), ajoute des données dans le `Model`, et retourne un **nom de vue logique** (ex : `"listPersonnes"`).
6. Le `DispatcherServlet` passe ce nom au **ViewResolver**.
7. Le `ViewResolver` résout le nom logique en chemin physique (ex : `/WEB-INF/views/listPersonnes.jsp`).
8. La vue JSP est rendue avec les données du `Model`.
9. La réponse HTML est renvoyée au navigateur.

⚠️ **Piège :** Si le stagiaire saute les étapes HandlerMapping / HandlerAdapter / ViewResolver, il n'a pas compris le flux interne.

---

**Q3 — Où est configuré le `DispatcherServlet` et quel fichier de contexte charge-t-il ?**

✅ Dans `web.xml`.  
Par convention, le `DispatcherServlet` nommé `dispatcher` charge automatiquement le fichier **`dispatcher-servlet.xml`** (ou `[nom]-servlet.xml`) situé dans `WEB-INF/`.  
Ce fichier déclare les beans Spring MVC : ViewResolver, scan des composants, etc.

⚠️ **Piège :** Certains confondent ce fichier avec `applicationContext.xml`. Les deux existent dans une app Spring MVC classique : `applicationContext.xml` pour les beans métier/persistence, `dispatcher-servlet.xml` pour la couche web.

---

**Q4 — ⚠️ PIÈGE — Le `DispatcherServlet` est-il un singleton ?**

✅ Oui, par défaut il existe **une seule instance** du `DispatcherServlet` dans la JVM pour toute l'application (pattern Singleton du conteneur de servlets).  
Il gère la concurrence car il est **stateless** : toutes les données de la requête sont stockées dans des objets locaux à chaque thread.

🚨 **Red flag :** Si le stagiaire dit que chaque requête crée une nouvelle instance, il n'a pas compris.

---

## 2. Comment Spring trouve le JSP (ViewResolver)

---

**Q5 — Comment Spring MVC sait-il où trouver le fichier JSP à afficher ?**

✅ Grâce au **`ViewResolver`**, configuré dans `dispatcher-servlet.xml` (ou classe Java `@Configuration`).  
Le `InternalResourceViewResolver` est le plus courant :

```xml
<!-- dispatcher-servlet.xml -->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/views/" />
    <property name="suffix" value=".jsp" />
</bean>
```

Quand un Controller retourne `"listPersonnes"`, Spring construit le chemin :  
`prefix + nom_logique + suffix` → `/WEB-INF/views/listPersonnes.jsp`

⚠️ **Piège :** Si le Controller retourne `"redirect:/chemin"`, le ViewResolver **n'est pas utilisé** — Spring envoie une redirection HTTP 302.

---

**Q6 — Pourquoi place-t-on les JSP dans `/WEB-INF/views/` et non à la racine ?**

✅ Le répertoire `WEB-INF/` est **non accessible directement** par le navigateur.  
Cela force toutes les requêtes à passer par le `DispatcherServlet` et les Controllers, empêchant l'accès direct aux vues sans passer par la logique applicative.

🚨 **Red flag :** Si le stagiaire n'a pas de raison claire, il n'a pas réfléchi à la sécurité de base.

---

**Q7 — ⚠️ PIÈGE — Un Controller peut-il retourner directement le contenu HTML sans ViewResolver ?**

✅ Oui, avec `@ResponseBody` (ou `@RestController`).  
Dans ce cas, Spring ne passe pas par le ViewResolver mais sérialise directement le retour (String, JSON, XML…) dans le corps de la réponse HTTP.

⚠️ Distinction importante : `@Controller` + ViewResolver → vue rendue. `@RestController` / `@ResponseBody` → réponse directe au client.

---

**Q8 — ⚠️ PIÈGE — Que se passe-t-il si le fichier JSP référencé n'existe pas ?**

✅ Spring lève une erreur HTTP **404** (ou `ServletException`) au moment du rendu de la vue.  
Le ViewResolver résout le nom → Spring essaie d'accéder au fichier → le serveur ne le trouve pas → 404.

---

## 3. IoC vs DI — Définitions et différences

---

**Q9 — Qu'est-ce que l'IoC (Inversion of Control) ?**

✅ L'IoC est un **principe de conception** : au lieu que l'objet crée ou recherche lui-même ses dépendances (`new MonService()`), c'est un **conteneur externe** (ici, Spring) qui prend le contrôle de la création et de l'assemblage des objets.

Analogie : au lieu que vous alliez chercher vos outils, quelqu'un vous les apporte.

```java
// SANS IoC — le Controller crée lui-même son service
public class PersonneController {
    private PersonneService service = new PersonneServiceImpl(); // ❌
}

// AVEC IoC — Spring crée et injecte le service
public class PersonneController {
    private PersonneService service; // Spring s'en occupe ✅
}
```

---

**Q10 — Qu'est-ce que la DI (Dependency Injection) ?**

✅ La DI est un **mécanisme** (une façon de réaliser l'IoC) : Spring **injecte** (fournit) les dépendances d'un objet de l'extérieur, soit via le constructeur, soit via un setter, soit via un champ.

⚠️ **Différence clé :**
- **IoC** = principe (qui contrôle la création des objets ?)
- **DI** = implémentation de l'IoC (comment les dépendances sont transmises ?)

> *L'IoC est l'objectif. La DI est le moyen.*

🚨 **Red flag :** Si le stagiaire dit « IoC et DI c'est la même chose », il confond le principe et son implémentation.

---

**Q11 — ⚠️ PIÈGE — L'IoC implique-t-il forcément la DI ?**

✅ Non. L'IoC peut se réaliser de plusieurs façons :
1. **Dependency Injection** (la plus courante, utilisée par Spring)
2. **Service Locator** (l'objet demande sa dépendance à un registre central)
3. **Factory Method** (une fabrique crée les objets)

Spring utilise la DI, mais l'IoC est le concept plus large.

---

**Q12 — Qu'est-ce qu'un Bean Spring ?**

✅ Un objet créé, configuré et géré par le **conteneur IoC de Spring** (l'`ApplicationContext`).  
Spring gère son cycle de vie complet : création → injection des dépendances → initialisation → utilisation → destruction.

⚠️ **Piège :** Un bean Spring n'est pas simplement « un objet Java ». C'est un objet dont le cycle de vie est délégué à Spring.

---

## 4. Types d'injection de dépendances

---

**Q13 — Quels sont les 3 types d'injection de dépendances en Spring ?**

✅

| Type | Comment | Annotation |
|---|---|---|
| **Par constructeur** | La dépendance est passée en paramètre du constructeur | `@Autowired` (optionnel si 1 seul constructeur depuis Spring 4.3) |
| **Par setter** | La dépendance est injectée via une méthode setter | `@Autowired` sur le setter |
| **Par champ** | La dépendance est injectée directement dans le champ | `@Autowired` sur le champ |

```java
// 1. INJECTION PAR CONSTRUCTEUR ✅ (recommandée)
@Controller
public class PersonneController {
    private final PersonneService personneService;

    public PersonneController(PersonneService personneService) {
        this.personneService = personneService;
    }
}

// 2. INJECTION PAR SETTER
@Controller
public class PersonneController {
    private PersonneService personneService;

    @Autowired
    public void setPersonneService(PersonneService personneService) {
        this.personneService = personneService;
    }
}

// 3. INJECTION PAR CHAMP ⚠️ (déconseillée en production)
@Controller
public class PersonneController {
    @Autowired
    private PersonneService personneService;
}
```

---

**Q14 — Quelle injection est recommandée et pourquoi ?**

✅ **L'injection par constructeur** est la pratique recommandée pour plusieurs raisons :

1. **Immuabilité** : on peut déclarer le champ `final`, la dépendance ne change jamais après construction.
2. **Testabilité** : on peut instancier l'objet dans un test unitaire sans Spring, en passant un mock directement au constructeur.
3. **Détection des erreurs au démarrage** : si une dépendance manque, Spring échoue au démarrage (fail-fast), pas en cours d'exécution.
4. **Dépendances explicites** : le constructeur documente clairement ce dont la classe a besoin.

⚠️ **Injection par champ** : pratique mais elle cache les dépendances, empêche l'utilisation de `final`, et rend les tests unitaires plus difficiles (il faut la réflexivité ou un framework de test pour injecter).

---

**Q15 — ⚠️ PIÈGE — Peut-on injecter une dépendance dans un Bean Spring sans `@Autowired` ?**

✅ Oui, de plusieurs façons :
1. **Constructeur unique** : depuis Spring 4.3, `@Autowired` est implicite sur un constructeur unique.
2. **`@Inject`** (JSR-330) : équivalent de `@Autowired`.
3. **`@Resource`** (JSR-250) : injection par nom de bean.
4. **XML** : configuration explicite avec `<property>` ou `<constructor-arg>`.

---

**Q16 — ⚠️ PIÈGE — `@Autowired` injecte-t-il par type ou par nom ?**

✅ Par défaut, **par type** (`byType`).  
Si plusieurs beans du même type existent, Spring lève une `NoUniqueBeanDefinitionException`.  
On peut lever l'ambiguïté avec :
- `@Qualifier("nomDuBean")` — précise quel bean injecter
- `@Primary` — désigne le bean préféré en cas d'ambiguïté

```java
@Autowired
@Qualifier("personneServiceV2")
private PersonneService personneService;
```

🚨 **Red flag :** Si le stagiaire ne connaît pas le mécanisme de résolution de Spring quand plusieurs beans du même type existent.

---

**Q17 — ⚠️ PIÈGE — Peut-on avoir une injection circulaire ? Que se passe-t-il ?**

✅ Oui, si `A` dépend de `B` et `B` dépend de `A`, on a une **dépendance circulaire**.  
- Avec l'injection par **champ ou setter** : Spring peut la résoudre (il crée d'abord les instances, puis injecte).
- Avec l'injection par **constructeur** : Spring **échoue au démarrage** avec `BeanCurrentlyInCreationException`.

✅ La bonne pratique : **éviter les dépendances circulaires** — elles révèlent un problème de conception (les deux classes sont trop couplées).

---

## 5. Architecture en couches

---

**Q18 — Quelles sont les couches de ton application et le rôle de chacune ?**

✅

| Couche | Rôle | Annotation |
|---|---|---|
| **Controller** | Reçoit la requête HTTP, délègue au Service, retourne une vue | `@Controller` |
| **Service** | Contient la **logique métier**, orchestre les appels Repository | `@Service` |
| **Repository** | Accès aux données (CRUD), abstraction de la base de données | `@Repository` |
| **Entité / Modèle** | Représentation des données métier, mappée en base via JPA | `@Entity` |

⚠️ **Règle d'or :** Le Controller ne parle qu'au Service. Le Service parle au Repository. Le Repository parle à la base. **Jamais de saut de couche.**

---

**Q19 — ⚠️ PIÈGE — Quelle est la différence entre `@Component`, `@Service`, `@Repository` et `@Controller` ?**

✅ Toutes les quatre sont des **spécialisations de `@Component`** (même fonctionnement de base : elles déclarent un bean Spring).  
La différence est **sémantique + comportementale** :

| Annotation | Couche | Comportement supplémentaire |
|---|---|---|
| `@Component` | Générique | Aucun |
| `@Controller` | Web (MVC) | Détecté par le `DispatcherServlet` comme gestionnaire de requêtes |
| `@Service` | Logique métier | Sémantique uniquement (aucun comportement supplémentaire par défaut) |
| `@Repository` | Accès données | **Traduit les exceptions JPA/Hibernate** en exceptions Spring (`DataAccessException`) |

🚨 **Red flag :** Répondre « c'est pareil, on peut utiliser `@Component` partout » — c'est vrai techniquement, mais révèle une méconnaissance des conventions et du comportement de `@Repository`.

---

**Q20 — Pourquoi ne doit-on pas mettre la logique métier dans le Controller ?**

✅ Plusieurs raisons :
1. **Violation du SRP** (Single Responsibility Principle) : le Controller a une seule responsabilité — gérer le flux HTTP.
2. **Non réutilisable** : si la même logique doit être appelée depuis un autre Controller ou un batch, on duplique le code.
3. **Non testable** : tester la logique métier nécessiterait de simuler tout le contexte HTTP.
4. **Couplage fort** : la logique métier devient dépendante du framework web.

---

**Q21 — ⚠️ PIÈGE — Le Repository peut-il appeler le Service ?**

✅ **Non.** Le flux est unidirectionnel : Controller → Service → Repository.  
Un Repository qui appelle un Service crée une dépendance inverse qui viole l'architecture en couches et crée généralement des dépendances circulaires.

🚨 **Red flag :** Si le stagiaire trouve ça normal ou ne voit pas le problème.

---

**Q22 — À quoi sert l'annotation `@Transactional` ? Où doit-on la placer ?**

✅ `@Transactional` délimite une **transaction** de base de données : toutes les opérations dans la méthode annotée s'exécutent dans une seule transaction.  
Si une exception survient, Spring effectue un **rollback** automatique.

**Bonne pratique :** La placer sur la couche **Service** (pas sur le Repository, pas sur le Controller).

```java
@Service
public class PersonneServiceImpl implements PersonneService {

    @Override
    @Transactional
    public void ajouterPersonne(Personne personne) {
        personneRepository.save(personne);
    }
}
```

⚠️ **Piège :** Par défaut, le rollback n'est déclenché que pour les `RuntimeException`. Pour les `Exception` vérifiées, il faut `@Transactional(rollbackFor = Exception.class)`.

---

## 6. Bonnes pratiques

---

**Q23 — Qu'est-ce que le principe « programming to an interface » et comment s'applique-t-il ici ?**

✅ On déclare les dépendances avec le **type interface**, pas l'implémentation concrète :

```java
// ✅ Bon : dépend de l'interface
private final PersonneService personneService;

// ❌ Mauvais : dépend de l'implémentation concrète
private final PersonneServiceImpl personneService;
```

Avantages : on peut changer l'implémentation sans modifier le Controller, facilite les mocks dans les tests.

---

**Q24 — ⚠️ PIÈGE — `@Autowired` est-il obligatoire en Spring moderne ?**

✅ Non. Depuis **Spring 4.3**, si une classe a **un seul constructeur**, Spring l'utilise automatiquement pour l'injection sans `@Autowired`.

```java
@Controller
public class PersonneController {
    private final PersonneService personneService;

    // @Autowired implicite depuis Spring 4.3
    public PersonneController(PersonneService personneService) {
        this.personneService = personneService;
    }
}
```

---

**Q25 — Pourquoi utiliser `final` sur les champs injectés par constructeur ?**

✅ Déclarer le champ `final` garantit que la dépendance ne peut pas être réaffectée après la construction de l'objet.  
Cela renforce l'**immuabilité** et empêche les bugs liés à un remplacement accidentel de la dépendance.

> `final` n'est possible qu'avec l'injection par constructeur — c'est une raison supplémentaire de la préférer.

---

**Q26 — Comment vérifies-tu que tes beans Spring sont bien créés au démarrage ?**

✅ Plusieurs approches :
- Lire les logs de démarrage Spring (chaque bean enregistré est loggué en mode DEBUG).
- Provoquer une injection manquante et observer la `NoSuchBeanDefinitionException` au démarrage.
- Utiliser `@PostConstruct` sur une méthode pour tracer l'initialisation d'un bean.
- Écrire un test d'intégration qui charge le contexte Spring et vérifie l'injection.

---

## 7. Questions pièges et challenges de compréhension

---

**Q27 — ⚠️ PIÈGE — Spring MVC et Spring Boot sont-ils la même chose ?**

✅ Non.
- **Spring MVC** est un framework web qui fait partie de Spring Framework.
- **Spring Boot** est une couche au-dessus de Spring Framework qui fournit l'auto-configuration, un serveur embarqué, et des starters pour éliminer la configuration manuelle.

Spring Boot *utilise* Spring MVC, mais Spring MVC peut fonctionner **sans Spring Boot** (c'est l'exercice réalisé).

---

**Q28 — ⚠️ PIÈGE — Que se passe-t-il si tu mets `@Autowired` sur un champ `private final` ?**

✅ Erreur de compilation : un champ `final` doit être initialisé à la déclaration ou dans le constructeur. `@Autowired` sur champ injecte après la construction, ce qui est incompatible avec `final`.

> C'est pourquoi `final` + injection = **obligatoirement par constructeur**.

🚨 **Red flag :** Si le stagiaire pense que `@Autowired` sur `final` fonctionne.

---

**Q29 — ⚠️ PIÈGE — Peut-on avoir plusieurs `DispatcherServlet` dans une application ?**

✅ Oui, techniquement on peut en déclarer plusieurs dans `web.xml` avec des URL patterns différents.  
Cas d'usage : séparer l'API REST (`/api/*`) du MVC classique (`/app/*`), chacun avec son contexte Spring propre.

En pratique, dans un exercice standard, un seul `DispatcherServlet` suffit.

---

**Q30 — ⚠️ PIÈGE — Si je ne mets pas `@Service` sur ma classe `PersonneServiceImpl`, que se passe-t-il ?**

✅ Spring ne crée pas de bean pour cette classe.  
Quand le Controller tente de l'injecter, Spring lève une `NoSuchBeanDefinitionException` au démarrage.

> Sauf si le bean est déclaré manuellement en XML avec `<bean id="personneService" class="...PersonneServiceImpl"/>`.

---

**Q31 — ⚠️ PIÈGE — `@ModelAttribute` dans la signature d'une méthode de Controller vs `@ModelAttribute` sur une méthode — quelle différence ?**

✅
- **Sur un paramètre de méthode** : lie les paramètres de la requête HTTP (formulaire) à un objet Java.

```java
@PostMapping("/ajouter")
public String ajouter(@ModelAttribute Personne personne) { ... }
```

- **Sur une méthode** (dans le Controller) : la méthode est exécutée **avant chaque handler** du Controller et ajoute son résultat au `Model`.

```java
@ModelAttribute("civilites")
public Civilite[] getCivilites() {
    return Civilite.values(); // disponible dans toutes les vues de ce Controller
}
```

⚠️ **Piège classique** : beaucoup ne connaissent que le premier usage.

---

**Q32 — ⚠️ PIÈGE FINAL — Quelle est la différence entre `redirect:` et `forward:` dans un return de Controller ?**

✅

| | `redirect:/url` | `forward:/url` |
|---|---|---|
| **HTTP** | Envoie un **302** au navigateur → nouvelle requête | Transfère **en interne** la requête (pas de nouvelle requête) |
| **URL** | L'URL du navigateur **change** | L'URL du navigateur **ne change pas** |
| **Données du Model** | Perdues (nouvelle requête) | Conservées |
| **Usage typique** | Après un POST (pattern PRG) | Passer le relai à un autre Controller |

> Pattern **PRG (Post-Redirect-Get)** : après un POST réussi, toujours faire `redirect:` pour éviter la double soumission du formulaire.

🚨 **Red flag :** Si le stagiaire ne connaît pas le pattern PRG et retourne une vue directement après un POST.

---

**Q33 — Comment dois-tu stocker les mots de passe en base de données ? Pourquoi ne jamais utiliser MD5 ?**

✅ Les mots de passe ne doivent **jamais** être stockés en clair ni hashés avec MD5 ou SHA-1.  
La bonne pratique est d'utiliser **BCrypt** (`BCryptPasswordEncoder` en Spring Security) :

- BCrypt est un **algorithme de hachage adaptatif** (le coût peut être augmenté avec le temps).
- Il intègre automatiquement un **sel aléatoire** à chaque hachage, rendant les attaques par rainbow table inefficaces.
- Deux hachages BCrypt du même mot de passe produisent des valeurs différentes.

MD5 est **cryptographiquement cassé** : des tables pré-calculées (rainbow tables) permettent de retrouver les mots de passe correspondants en quelques secondes.

```java
// Hachage à l'enregistrement
BCryptPasswordEncoder encoder = new BCryptPasswordEncoder();
String hash = encoder.encode("motDePasse"); // à stocker en base

// Vérification à l'authentification
boolean ok = encoder.matches("motDePasse", hashStockeEnBase);
```

🚨 **Red flag critique :** Tout stagiaire qui dit « je stocke le mot de passe en clair » ou « j'utilise MD5 » révèle une méconnaissance fondamentale de la sécurité.

---

## Récapitulatif — Signaux d'alerte rapides

| 🚨 Signal | Ce que ça révèle |
|---|---|
| Ne peut pas expliquer le rôle du `DispatcherServlet` | N'a pas compris Spring MVC |
| Dit « IoC et DI c'est pareil » | Concepts de base mal assimilés |
| Utilise `new MonService()` dans le Controller | N'a pas compris l'IoC |
| Logique métier dans le Controller | Architecture non respectée |
| Mot de passe stocké en clair ou MD5 | Méconnaissance sécurité critique |
| Ne sait pas pourquoi JSP est dans `/WEB-INF/` | N'a pas réfléchi à la sécurité |
| Préfère l'injection par champ sans raison | Pratique sans compréhension |
| Ne connaît pas `@Transactional` | Lacune persistance importante |
| Retourne une vue JSP après un POST sans `redirect:` | Ne connaît pas le pattern PRG |
| Ne sait pas ce que fait `@Repository` de plus que `@Component` | Annotations utilisées sans compréhension |
