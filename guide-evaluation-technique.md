# Guide d'Évaluation Technique — Entretien Stagiaire PFE
## Spring MVC (sans Spring Boot) + MySQL

---

## 1. Introduction

### Objectif de l'évaluation

Ce guide est destiné à l'évaluateur lors d'un **entretien technique de 30 minutes** avec un stagiaire PFE ayant réalisé un exercice de développement d'une application web en architecture **MVC classique** avec **Spring MVC** et **MySQL**.

L'objectif est de vérifier que le stagiaire comprend **réellement** les technologies qu'il a utilisées, et non qu'il les a simplement copiées ou suivies sans comprendre.

### Durée de l'entretien

**30 minutes** réparties comme suit :

| Phase | Durée | Contenu |
|---|---|---|
| Présentation du projet | 3 min | Le stagiaire présente son travail |
| Questions architecture & Spring | 15 min | Questions techniques ciblées |
| Exercice live | 10 min | 1 à 2 exercices pratiques |
| Bilan & questions du stagiaire | 2 min | Conclusion |

### Compétences évaluées

- Architecture en couches (MVC / Controller / Service / Repository)
- Spring Core (IoC, Injection de Dépendances)
- Spring MVC (DispatcherServlet, ViewResolver, annotations)
- Persistance JPA/Hibernate (entités, relations, JPQL)
- Validation des données (Bean Validation, BindingResult)
- Gestion de la sécurité (rôles, mots de passe, sessions)
- Qualité et lisibilité du code

---

## 2. Rappel de l'exercice

### Description de l'exercice

L'exercice demandait de développer une application web en **Spring MVC (sans Spring Boot)** avec les fonctionnalités suivantes :

#### Entités de base de données MySQL

**Entité `Utilisateur`**
- `login`
- `mot de passe`
- `rôle` : `Admin` ou `Utilisateur normal`

**Entité `Personne`**
- `nom`
- `prénom`
- `civilité` : `M.`, `Mme`, `Melle`
- `adresse`
- `date de naissance`

#### Authentification

- Écran de connexion (Spring Security optionnel)
- Récupération du rôle depuis l'entité `Utilisateur` après connexion

| Rôle Admin | Rôle Utilisateur normal |
|---|---|
| Recherche personne | Recherche personne |
| Ajout personne | Ajout personne uniquement |
| Modification personne | ✗ |
| Suppression personne | ✗ |

#### Recherche de personnes

Critères (opérateur AND) : nom contient, prénom contient, civilité = valeur

Résultat : tableau avec nom (cliquable → détail), prénom, civilité, adresse

#### Écran détail / modification

Champs : nom, prénom, civilité, adresse, date de naissance

Validations :
- Date de naissance au format `JJ/MM/AAAA`
- Nom obligatoire
- Prénom obligatoire

### Objectifs techniques

- Maîtrise de l'architecture MVC en couches
- Utilisation correcte des annotations Spring
- Implémentation d'une logique métier dans la couche service
- Requêtes conditionnelles avec JPA/Hibernate
- Validation côté serveur
- Gestion basique des droits d'accès par rôle

---

## 3. Déroulement complet de l'entretien (30 minutes)

### ⏱ Phase 1 — Présentation du projet (0–3 min)

**But :** Laisser le stagiaire présenter son travail librement.

Questions d'amorce :
- « Présente-moi ton application en 2-3 phrases. »
- « Comment as-tu organisé ton projet ? Quels packages as-tu créés ? »
- « Quelles ont été les difficultés que tu as rencontrées ? »

**Ce qu'on observe :** Clarté d'expression, structure du projet, conscience des difficultés rencontrées.

---

### ⏱ Phase 2 — Architecture (3–8 min)

**But :** Vérifier la compréhension de l'architecture en couches.

- Explique-moi les différentes couches de ton application.
- Quel est le rôle du Controller ? Du Service ? Du Repository ?
- Pourquoi ne pas mettre la logique métier directement dans le Controller ?
- Qu'est-ce que le principe de séparation des responsabilités ?

---

### ⏱ Phase 3 — Spring Core & Injection de Dépendances (8–12 min)

**But :** Vérifier la compréhension de l'IoC et de l'injection de dépendances.

- Qu'est-ce que l'IoC (Inversion of Control) ?
- Qu'est-ce que l'injection de dépendances ?
- Quelle est la différence entre `@Component`, `@Service`, `@Repository`, `@Controller` ?
- Quand utilises-tu `@Autowired` ? Y a-t-il d'autres façons d'injecter ?

---

### ⏱ Phase 4 — Spring MVC (12–18 min)

**But :** Vérifier la maîtrise du cycle requête/réponse Spring MVC.

- Comment fonctionne le `DispatcherServlet` ?
- Qu'est-ce que le `ViewResolver` ?
- À quoi sert `@RequestMapping` ? Et `@GetMapping` / `@PostMapping` ?
- Comment passes-tu des données du Controller à la vue ?
- Quelle est la différence entre `Model`, `ModelMap` et `ModelAndView` ?
- À quoi sert `@ModelAttribute` ?

---

### ⏱ Phase 5 — Persistance JPA/Hibernate (18–22 min)

**But :** Vérifier la compréhension de la persistance des données.

- Comment as-tu mappé tes entités ?
- Comment as-tu modélisé les enums (`Civilité`, `Rôle`) ?
- Comment as-tu géré la date de naissance ?
- Quelle est la différence entre une entité JPA et un DTO ?
- Comment fonctionne le cycle de vie d'une entité JPA ?

---

### ⏱ Phase 6 — Recherche & Validation (22–26 min)

**But :** Vérifier la gestion des critères de recherche et la validation.

- Comment as-tu géré la recherche avec plusieurs critères optionnels ?
- Comment fonctionne l'opérateur `LIKE` en JPQL ?
- Qu'est-ce que `@Valid` et `BindingResult` ?
- Quelle est la différence entre validation côté client et côté serveur ?

---

### ⏱ Phase 7 — Exercice live (26–30 min)

**But :** Tester la capacité du stagiaire à identifier et corriger des problèmes en conditions réelles.

→ Choisir 1 ou 2 exercices parmi ceux décrits en **Section 6**.

---

## 4. Questions techniques détaillées

### 4.1 Architecture

| Question | Réponse attendue | Points à vérifier | Remarques |
|---|---|---|---|
| Quelles sont les couches de ton application ? | Controller → Service → Repository → Base de données | Le stagiaire doit nommer les 3 couches et décrire leur rôle | ⚠️ Red flag si la réponse se limite à « MVC » sans détailler |
| Quel est le rôle du Controller ? | Recevoir les requêtes HTTP, déléguer au Service, retourner une vue | Ne contient pas de logique métier | ⚠️ Red flag si le stagiaire dit « le Controller fait tout » |
| Quel est le rôle du Service ? | Contenir la logique métier, orchestrer les appels Repository | Peut contenir des validations, transformations | ⚠️ Red flag si le stagiaire ne comprend pas pourquoi on sépare |
| Quel est le rôle du Repository ? | Accès aux données (CRUD), abstraction de la base de données | Utilise JPA/Hibernate, pas de logique métier | ⚠️ Red flag si le stagiaire confond avec le Service |
| Pourquoi ne pas mettre la logique métier dans le Controller ? | Violation du SRP, difficile à tester, difficile à réutiliser | Comprend les principes SOLID | ✅ Bon profil si mentionne testabilité et réutilisabilité |

---

### 4.2 Spring Core

| Question | Réponse attendue | Points à vérifier | Remarques |
|---|---|---|---|
| Qu'est-ce que l'IoC ? | Inversion du contrôle de création des objets — c'est le framework qui gère le cycle de vie des beans | Comprend que le développeur ne fait pas `new` manuellement | ⚠️ Red flag si confond IoC et DI |
| Qu'est-ce que l'injection de dépendances ? | Fournir les dépendances d'un objet de l'extérieur (via constructeur, setter ou champ) plutôt que de les instancier | Peut donner un exemple concret | ✅ Bon profil si mentionne l'injection par constructeur comme meilleure pratique |
| Différence entre `@Component`, `@Service`, `@Repository`, `@Controller` ? | Toutes dérivent de `@Component`. `@Service` = logique métier, `@Repository` = accès données (+ gestion exceptions), `@Controller` = couche web | Comprend la sémantique et pas uniquement le fait que ce sont des beans | ⚠️ Red flag si répond « c'est pareil » sans nuance |
| Préfères-tu `@Autowired` sur champ ou injection par constructeur ? Pourquoi ? | Injection par constructeur : permet l'immuabilité, facilite les tests unitaires, erreurs détectées au démarrage | Comprend les avantages de l'injection par constructeur | ✅ Excellent profil si mentionne qu'avec Spring 4.3+, `@Autowired` est optionnel sur constructeur unique |
| Qu'est-ce qu'un Bean Spring ? | Un objet géré par le conteneur IoC de Spring | Comprend le cycle de vie (création, injection, destruction) | ⚠️ Red flag si répond uniquement « un objet » sans mentionner le conteneur |

---

### 4.3 Spring MVC

| Question | Réponse attendue | Points à vérifier | Remarques |
|---|---|---|---|
| Qu'est-ce que le `DispatcherServlet` ? | Le Front Controller de Spring MVC — reçoit toutes les requêtes HTTP et les délègue au bon Controller | Comprend le pattern Front Controller | ⚠️ Red flag si ne peut pas expliquer le rôle du DispatcherServlet |
| Qu'est-ce que le `ViewResolver` ? | Composant qui résout le nom logique d'une vue vers une vue physique (ex : `home` → `/WEB-INF/views/home.jsp`) | Comprend la séparation entre nom logique et chemin physique | ✅ Bon profil si mentionne la configuration dans `servlet-context.xml` |
| À quoi sert `@RequestMapping` ? | Mapper une URL (et/ou une méthode HTTP) à une méthode de Controller | Peut différencier `@GetMapping`, `@PostMapping`, etc. | ✅ Bon profil si connaît les variantes raccourcies |
| Comment passes-tu des données à la vue ? | Via `Model.addAttribute()`, `ModelAndView`, ou `@ModelAttribute` | Comprend la différence entre les approches | ⚠️ Red flag si ne sait pas comment transmettre des données |
| Différence entre `Model` et `ModelAndView` ? | `Model` : ne porte que les attributs. `ModelAndView` : porte à la fois les attributs et le nom de la vue | Comprend quand utiliser l'un ou l'autre | ✅ Bon profil si précise que `Model` est injecté par Spring |
| À quoi sert `@ModelAttribute` ? | Lier les paramètres de formulaire à un objet Java, ou pré-peupler le modèle | Comprend son usage dans les formulaires | ⚠️ Red flag si ne comprend pas comment les formulaires sont liés aux objets Java |

---

### 4.4 Persistance

| Question | Réponse attendue | Points à vérifier | Remarques |
|---|---|---|---|
| Comment as-tu annoté tes entités JPA ? | `@Entity`, `@Id`, `@GeneratedValue`, `@Column`, `@Enumerated`, `@Temporal` (pour les dates) | Connaît les annotations de base | ⚠️ Red flag si ne connaît pas `@Entity` ou `@Id` |
| Comment as-tu modélisé les enums (`Civilité`, `Rôle`) ? | Avec `@Enumerated(EnumType.STRING)` pour stocker la valeur lisible en base | Comprend la différence entre `ORDINAL` et `STRING` | ⚠️ Red flag si utilise `EnumType.ORDINAL` sans comprendre les risques |
| Comment as-tu géré la date de naissance ? | Avec `@Temporal(TemporalType.DATE)` ou `LocalDate` (JPA 2.2+) | Comprend les types de date JPA | ⚠️ Red flag si stocke la date comme une `String` |
| Différence entre une entité JPA et un DTO ? | Entité : liée à la base de données, gérée par JPA. DTO : objet de transfert sans lien avec JPA | Comprend pourquoi on évite d'exposer les entités directement | ✅ Bon profil si mentionne les risques de sérialisation des entités |
| Qu'est-ce que la session Hibernate ? Comment la gère-t-on dans Spring ? | Session = contexte de persistance Hibernate. Spring la gère via `@Transactional` | Comprend le lien entre `@Transactional` et la session | ⚠️ Red flag si ne connaît pas `@Transactional` |

---

### 4.5 Recherche

| Question | Réponse attendue | Points à vérifier | Remarques |
|---|---|---|---|
| Comment as-tu implémenté la recherche avec plusieurs critères ? | Requête JPQL/HQL dynamique, ou utilisation de `JpaSpecificationExecutor`, ou requête avec paramètres conditionnels | Comprend la recherche conditionnelle | ⚠️ Red flag si construit la requête par concaténation de String SQL directe |
| Comment fonctionne l'opérateur `LIKE` en JPQL ? | `WHERE e.nom LIKE :nom` avec paramètre `'%valeur%'` | Comprend la syntaxe JPQL | ✅ Bon profil si mentionne `LOWER()` pour la recherche insensible à la casse |
| Comment gères-tu les critères optionnels (valeur vide ou null) ? | Ignorer le critère si la valeur est vide, ou utiliser `Criteria API` / `Specification` | Comprend la gestion des filtres dynamiques | ⚠️ Red flag si ajoute toujours tous les critères même vides |
| As-tu prévu une pagination des résultats ? | Utilisation de `Pageable`, `Page`, ou pagination manuelle JPQL avec `setFirstResult` / `setMaxResults` | Comprend pourquoi la pagination est importante | ✅ Bon profil si mentionne les risques de performance sans pagination |

---

### 4.6 Validation

| Question | Réponse attendue | Points à vérifier | Remarques |
|---|---|---|---|
| Quelles validations as-tu implémentées ? | Nom et prénom obligatoires, date de naissance au format `JJ/MM/AAAA` | Connaît les règles de validation de l'exercice | ⚠️ Red flag si n'a implémenté aucune validation |
| Qu'est-ce que `@Valid` et `BindingResult` ? | `@Valid` déclenche la validation Bean Validation. `BindingResult` contient les erreurs de validation | Comprend le mécanisme de validation Spring MVC | ⚠️ Red flag si ne connaît pas `BindingResult` |
| Quelles annotations de validation connais-tu ? | `@NotNull`, `@NotBlank`, `@Size`, `@Pattern`, `@Min`, `@Max`, `@Past`, `@Future` | Connaît les annotations Bean Validation standard | ✅ Bon profil si mentionne `@Pattern` pour la date |
| Différence entre validation côté client et côté serveur ? | Client = JavaScript, UX rapide mais contournable. Serveur = fiable, indispensable pour la sécurité | Comprend pourquoi la validation serveur est obligatoire | ✅ Bon profil si mentionne que la validation client est optionnelle mais améliore l'UX |
| Comment affiches-tu les erreurs de validation dans la vue JSP ? | Avec les balises Spring `<form:errors path="nom" />` | Comprend l'intégration vue/validation | ⚠️ Red flag si ne sait pas comment afficher les erreurs |

---

### 4.7 Sécurité

| Question | Réponse attendue | Points à vérifier | Remarques |
|---|---|---|---|
| Comment as-tu géré l'authentification ? | Session HTTP, ou Spring Security si utilisé. Vérification du login/mot de passe en base | Comprend le mécanisme d'authentification | ⚠️ Red flag si compare le mot de passe en clair |
| Comment stockes-tu les mots de passe ? | Hashés avec BCrypt (`BCryptPasswordEncoder`) | Comprend pourquoi on ne stocke jamais en clair | ⚠️ Red flag critique si dit « en clair » ou « MD5 » |
| Comment as-tu géré les rôles et restrictions d'accès ? | Via la session (stockage du rôle), vérification dans le Controller ou via Spring Security | Comprend la gestion des autorisations | ✅ Bon profil si mentionne Spring Security avec `@PreAuthorize` ou config XML |
| Que se passe-t-il si un utilisateur normal accède directement à l'URL de suppression ? | Sans protection, il peut accéder. Avec Spring Security ou vérification manuelle du rôle, il est bloqué | Comprend la nécessité de protection côté serveur | ⚠️ Red flag si pense que l'interface suffit à sécuriser |

---

## 5. Questions avancées

Ces questions permettent d'identifier les profils les plus solides. Le stagiaire n'est pas nécessairement censé répondre parfaitement, mais sa façon d'approcher les questions révèle sa maturité technique.

| Question | Réponse attendue | Points à vérifier | Remarques |
|---|---|---|---|
| Si demain tu devais migrer vers Spring Boot, qu'est-ce qui changerait ? | Suppression de la config XML, auto-configuration, `application.properties` | Comprend la valeur ajoutée de Spring Boot | ✅ Bon signe si comprend que Spring Boot est une couche au-dessus de Spring |
| Comment améliorerais-tu les performances de la recherche ? | Index SQL sur les colonnes recherchées, cache de second niveau Hibernate, pagination | Pense à la performance | ✅ Excellent profil si mentionne les index et la pagination comme premières optimisations |
| Qu'est-ce que le pattern DTO et pourquoi l'utiliser ? | Objet de transfert entre couches. Évite d'exposer les entités, découple la vue du modèle | Comprend la séparation des couches | ✅ Bon profil si mentionne des frameworks de mapping comme MapStruct |
| Comment gérerais-tu les erreurs globalement dans ton application ? | `@ControllerAdvice` avec `@ExceptionHandler`, pages d'erreur personnalisées | Comprend la gestion centralisée des erreurs | ✅ Bon profil si mentionne `@ControllerAdvice` |
| Comment écrirais-tu des tests unitaires pour ta couche Service ? | Avec JUnit et Mockito, en mockant le Repository | Comprend les tests unitaires et le mocking | ✅ Excellent profil si mentionne Mockito et l'importance d'isoler les couches |
| Qu'est-ce que le problème N+1 en JPA et comment le résoudre ? | Chargement lazy qui génère N requêtes supplémentaires. Se résout avec `JOIN FETCH` ou `@EntityGraph` | Comprend les pièges classiques de JPA | ✅ Excellent profil — question difficile pour un stagiaire |
| Qu'est-ce que CSRF et comment Spring Security le gère-t-il ? | Cross-Site Request Forgery. Spring Security génère un token CSRF dans les formulaires | Connaissance de la sécurité web | ✅ Excellent profil — bonus |

---

## 6. Exercices LIVE pour l'entretien

Choisir **1 ou 2 exercices** parmi les suivants en fonction du temps disponible.

---

### Exercice 1 — Corriger un Controller mal structuré

**Objectif :** Vérifier que le stagiaire identifie les violations de l'architecture MVC.

**Niveau :** ⭐⭐ Moyen

**Énoncé :**

> Voici un extrait de code. Identifie les problèmes et propose une correction.

```java
@Controller
@RequestMapping("/personnes")
public class PersonneController {

    @Autowired
    private PersonneRepository personneRepository;

    @PostMapping("/ajouter")
    public String ajouterPersonne(@ModelAttribute Personne personne) {
        // Vérification du nom
        if (personne.getNom() == null || personne.getNom().isEmpty()) {
            return "formulaire";
        }
        // Hachage du mot de passe de l'utilisateur connecté
        String mdpHash = MD5Utils.hash(personne.getMotDePasse());
        personne.setMotDePasse(mdpHash);
        // Sauvegarde directe
        personneRepository.save(personne);
        return "redirect:/personnes";
    }
}
```

**Problèmes à identifier :**

1. Le Controller utilise directement le Repository → violation de l'architecture en couches (pas de couche Service)
2. Logique métier (validation, hachage) dans le Controller → doit être dans le Service
3. Utilisation de MD5 pour le hachage → MD5 est obsolète et non sécurisé, utiliser **BCrypt**
4. Absence de `@Valid` et `BindingResult` pour la validation Bean Validation

**Solution attendue :**

```java
@Controller
@RequestMapping("/personnes")
public class PersonneController {

    private final PersonneService personneService;

    public PersonneController(PersonneService personneService) {
        this.personneService = personneService;
    }

    @PostMapping("/ajouter")
    public String ajouterPersonne(@Valid @ModelAttribute Personne personne,
                                   BindingResult result) {
        if (result.hasErrors()) {
            return "formulaire";
        }
        personneService.ajouterPersonne(personne);
        return "redirect:/personnes";
    }
}
```

```java
@Service
public class PersonneServiceImpl implements PersonneService {

    private final PersonneRepository personneRepository;
    private final BCryptPasswordEncoder passwordEncoder;

    public PersonneServiceImpl(PersonneRepository personneRepository,
                                BCryptPasswordEncoder passwordEncoder) {
        this.personneRepository = personneRepository;
        this.passwordEncoder = passwordEncoder;
    }

    @Override
    @Transactional
    public void ajouterPersonne(Personne personne) {
        personneRepository.save(personne);
    }
}
```

---

### Exercice 2 — Ajouter une validation

**Objectif :** Vérifier que le stagiaire sait ajouter une validation Bean Validation sur une entité.

**Niveau :** ⭐ Facile

**Énoncé :**

> Voici l'entité `Personne`. Ajoute les validations suivantes :
> - `nom` : obligatoire, 2 à 100 caractères
> - `prenom` : obligatoire, 2 à 100 caractères
> - `dateNaissance` : obligatoire, doit être dans le passé
> - `civilite` : obligatoire

```java
@Entity
public class Personne {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String nom;
    private String prenom;

    @Enumerated(EnumType.STRING)
    private Civilite civilite;

    private String adresse;

    @Temporal(TemporalType.DATE)
    private Date dateNaissance;

    // getters / setters
}
```

**Solution attendue :**

```java
@Entity
public class Personne {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @NotBlank(message = "Le nom est obligatoire")
    @Size(min = 2, max = 100, message = "Le nom doit contenir entre 2 et 100 caractères")
    private String nom;

    @NotBlank(message = "Le prénom est obligatoire")
    @Size(min = 2, max = 100, message = "Le prénom doit contenir entre 2 et 100 caractères")
    private String prenom;

    @NotNull(message = "La civilité est obligatoire")
    @Enumerated(EnumType.STRING)
    private Civilite civilite;

    private String adresse;

    @NotNull(message = "La date de naissance est obligatoire")
    @Past(message = "La date de naissance doit être dans le passé")
    @Temporal(TemporalType.DATE)
    private Date dateNaissance;

    // getters / setters
}
```

---

### Exercice 3 — Écrire une requête de recherche dynamique

**Objectif :** Vérifier que le stagiaire sait écrire une requête JPQL avec des critères optionnels.

**Niveau :** ⭐⭐⭐ Difficile

**Énoncé :**

> Implémente la méthode de recherche suivante dans le Repository ou le Service. Les critères sont optionnels (peuvent être vides ou null).
>
> Critères : `nom` (contient), `prenom` (contient), `civilite` (égalité)

**Solution attendue (Repository avec JPQL) :**

```java
@Repository
public class PersonneRepositoryImpl implements PersonneRepositoryCustom {

    @PersistenceContext
    private EntityManager entityManager;

    @Override
    public List<Personne> rechercher(String nom, String prenom, Civilite civilite) {
        StringBuilder jpql = new StringBuilder("SELECT p FROM Personne p WHERE 1=1");

        Map<String, Object> params = new LinkedHashMap<>();

        if (nom != null && !nom.isEmpty()) {
            jpql.append(" AND LOWER(p.nom) LIKE :nom");
            params.put("nom", "%" + nom.toLowerCase() + "%");
        }
        if (prenom != null && !prenom.isEmpty()) {
            jpql.append(" AND LOWER(p.prenom) LIKE :prenom");
            params.put("prenom", "%" + prenom.toLowerCase() + "%");
        }
        if (civilite != null) {
            jpql.append(" AND p.civilite = :civilite");
            params.put("civilite", civilite);
        }

        TypedQuery<Personne> query = entityManager.createQuery(jpql.toString(), Personne.class);
        params.forEach(query::setParameter);

        return query.getResultList();
    }
}
```

**Points à vérifier :**
- Utilisation de paramètres nommés (pas de concaténation directe → prévention SQL Injection)
- `1=1` pour simplifier la construction dynamique
- `LOWER()` pour la recherche insensible à la casse
- Vérification null ET chaîne vide

---

## 7. Signaux d'alerte (Red Flags)

Ces comportements indiquent que le stagiaire n'a pas réellement compris les technologies utilisées.

### 🚨 Red Flags critiques (éliminatoires)

| Signal | Signification | Question de détection |
|---|---|---|
| Mot de passe stocké en clair ou avec MD5 | Méconnaissance des bases de sécurité | « Comment stockes-tu les mots de passe ? » |
| Incapacité totale à expliquer le `DispatcherServlet` | N'a pas compris le fonctionnement de Spring MVC | « Que se passe-t-il quand une requête HTTP arrive sur ton application ? » |
| Logique métier entièrement dans le Controller | Pas de compréhension de l'architecture en couches | « Montre-moi où tu as mis la logique de validation ou de transformation des données » |
| Injection de dépendances par `new` | N'a pas compris l'IoC | « Comment crées-tu tes objets Service dans le Controller ? » |

### ⚠️ Red Flags importants

| Signal | Signification | Question de détection |
|---|---|---|
| Absence de couche Service | Architecture non respectée | « Où se trouve la logique métier de ton application ? » |
| Confusion entre IoC et DI | Concepts de base non maîtrisés | « Quelle est la différence entre IoC et injection de dépendances ? » |
| Utilisation de `@Autowired` sur champ sans explication | Pratique déconseillée sans conscience | « Pourquoi utilises-tu `@Autowired` sur le champ plutôt que sur le constructeur ? » |
| Entités JPA directement exposées dans les vues | Pas de séparation des couches | « Passes-tu directement tes entités JPA aux vues ? » |
| Pas de validation côté serveur | Compréhension incomplète de la sécurité | « Si un utilisateur désactive JavaScript, tes validations fonctionnent encore ? » |
| Requêtes SQL construites par concaténation | Risque de SQL Injection | « Comment construis-tu ta requête de recherche dynamique ? » |
| Ne sait pas ce qu'est `@Transactional` | Lacune importante sur la persistance | « Comment gères-tu les transactions dans ton application ? » |

### ℹ️ Signes à surveiller (non éliminatoires mais révélateurs)

| Signal | Interprétation |
|---|---|
| Ne peut pas expliquer pourquoi il a fait un choix technique | A suivi un tutoriel sans comprendre |
| Réponses très courtes sans pouvoir développer | Connaissances superficielles |
| Confond Spring MVC et Spring Boot | Manque de clarté sur les technologies |
| N'a pas prévu de pagination | N'a pas réfléchi aux aspects production |
| Aucune gestion des erreurs (try/catch ou @ControllerAdvice) | Application fragile |

---

## 8. Grille d'évaluation finale

### Tableau de notation

| Critère | Éléments évalués | Score max | Score obtenu |
|---|---|---|---|
| **Architecture** | Maîtrise des couches Controller/Service/Repository, SRP, séparation des responsabilités | 5 | |
| **Spring Core** | IoC, injection de dépendances, annotations `@Component` / `@Service` / `@Repository`, cycles de vie des beans | 5 | |
| **Spring MVC** | DispatcherServlet, ViewResolver, `@RequestMapping`, `@ModelAttribute`, `Model` vs `ModelAndView` | 5 | |
| **Persistance** | JPA/Hibernate, entités, enums, dates, JPQL, `@Transactional` | 5 | |
| **Logique métier & qualité** | Validation Bean Validation, `@Valid` / `BindingResult`, gestion de la recherche dynamique, absence de code smell | 5 | |
| **Compréhension globale & sécurité** | Gestion des rôles, stockage des mots de passe (BCrypt), protection des accès, cohérence globale | 5 | |
| **TOTAL** | | **30** | |

---

### Barème d'interprétation

| Score | Appréciation | Recommandation |
|---|---|---|
| 25 – 30 | Excellent | Profil solide, autonomie complète possible |
| 18 – 24 | Bien | Bonnes bases, quelques lacunes à combler |
| 12 – 17 | Passable | Compréhension partielle, encadrement nécessaire |
| 6 – 11 | Insuffisant | Bases fragiles, travail significatif requis |
| 0 – 5 | Insuffisant critique | N'a pas compris les technologies utilisées |

---

### Notes de l'évaluateur

| Section | Observations |
|---|---|
| Points forts | |
| Points faibles | |
| Red Flags détectés | |
| Décision | ✅ Validé / ⚠️ Conditionnel / ❌ Non validé |

---

*Document à usage interne — Évaluation technique stagiaire PFE*
