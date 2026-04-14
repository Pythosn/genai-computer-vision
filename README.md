# Generative AI en Computer Vision

---

## Ce que c'est vraiment

La plupart des gens pensent que l'IA "cherche" une image dans une base de données quand on lui demande d'en créer une. C'est logique comme intuition — mais c'est faux.

Un modèle génératif ne stocke pas d'images. Il apprend des **patterns** — les formes qui reviennent, les textures, les relations entre les objets — et à partir de ça, il devient capable d'assembler quelque chose de nouveau. Comme un musicien qui a écouté des milliers de morceaux et qui peut improviser sur scène sans jamais recopier quoi que ce soit.

C'est ça la Generative AI en Computer Vision : un système qui a appris à voir, et qui utilise cette compréhension pour créer.

---

## Pourquoi ça change quelque chose

Avant, produire une image demandait soit un humain (dessinateur, photographe), soit une banque d'images existante. Les deux ont une limite commune : tu ne peux montrer que ce qui existe déjà.

La GenAI casse cette règle.

Quelques cas où ça devient concret :

**En médecine**, certaines maladies sont si rares qu'on n'a pas assez d'images pour entraîner des modèles de diagnostic. On peut maintenant en générer artificiellement — des images synthétiques mais réalistes, qui permettent d'entraîner des outils qui sauvent des vies.

**En sécurité informatique**, on génère de fausses attaques visuelles pour tester des systèmes de défense avant qu'une vraie menace n'arrive. C'est de l'entraînement au combat sans risque réel.

**En design et e-commerce**, une description textuelle suffit à produire des centaines de variantes visuelles en quelques minutes. Pas de photographe, pas de studio.

Ce n'est pas juste un gain de temps. C'est une capacité qui n'existait tout simplement pas avant.

---

## Les trois façons de faire

Il n'y a pas qu'une seule manière de construire un modèle génératif. Trois architectures dominent, et elles partent chacune d'une intuition très différente.

---

### GAN — deux réseaux qui se battent

L'idée de base est presque trop simple : faire travailler deux réseaux l'un contre l'autre.

Le premier, le **générateur**, part de bruit aléatoire et essaie de produire une image crédible. Le second, le **discriminateur**, reçoit un mélange d'images réelles et d'images générées, et doit les distinguer. Le générateur essaie de tromper le discriminateur. Le discriminateur essaie de ne pas se faire avoir. Ils s'améliorent ensemble.

```mermaid
flowchart LR
    A[Bruit aléatoire] --> B[Générateur]
    B --> C[Image générée]
    D[Image réelle] --> E[Discriminateur]
    C --> E
    E --> F{Vraie ou fausse ?}
    F -.->|Retour au générateur| B
```

Pense à un faussaire en monnaie et un expert de la banque centrale. Le faussaire améliore ses billets, l'expert affine ses tests — jusqu'à ce que les faux soient quasi indétectables. C'est exactement la dynamique d'un GAN.

Ce qui rend le GAN puissant, c'est aussi ce qui le rend difficile : l'équilibre entre les deux réseaux est fragile. Si le discriminateur devient trop fort trop vite, le générateur ne reçoit plus de signal utile pour s'améliorer.

**Avantages :** images très réalistes, génération rapide.

**Limites :** entraînement instable, risque de *mode collapse* (le générateur trouve une seule image qui trompe bien le discriminateur et arrête d'explorer autre chose), contrôle limité.

**Exemple :** StyleGAN de NVIDIA — chaque visage sur thispersondoesnotexist.com est généré par un GAN. Aucun de ces gens n'existe.

---

### VAE — comprendre pour reconstruire

Le VAE part d'une idée différente : plutôt que d'opposer deux réseaux, on va apprendre à **comprendre la structure** des images.

L'encodeur prend une image et la comprime en un petit résumé numérique — un peu comme si tu décrivais une peinture à quelqu'un au téléphone. Ce résumé vit dans ce qu'on appelle un *espace latent* (une sorte de carte où chaque point correspond à une image possible). Le décodeur prend ces coordonnées et reconstruit l'image.

```mermaid
flowchart LR
    A[Image d'entrée] --> B[Encodeur]
    B --> C[Espace latent]
    C --> D[Décodeur]
    D --> E[Image reconstruite]
```

Ce qui est intéressant : l'espace latent est organisé. Des images similaires se retrouvent proches sur cette carte. Ça veut dire qu'on peut naviguer progressivement entre deux images — passer d'un visage souriant à un visage sérieux, étape par étape — ou modifier une seule caractéristique sans toucher au reste.

**Avantages :** entraînement stable, espace latent navigable, mathématiquement solide.

**Limites :** images parfois un peu floues, contrôle textuel faible.

**Exemple :** génération d'images médicales synthétiques pour augmenter des datasets de maladies rares.

---

### Diffusion — partir du chaos

Les modèles de diffusion sont les plus récents des trois, et ils dominent aujourd'hui le marché. L'idée de départ est surprenante.

Pendant l'entraînement, on prend des images réelles et on les détruit progressivement en ajoutant du bruit — jusqu'à ce qu'il ne reste plus rien d'identifiable. Le modèle apprend à inverser ce processus : étant donné une image bruitée, enlever ce bruit, étape par étape.

```mermaid
flowchart LR
    subgraph Forward [Ajouter du bruit]
        A[Image nette] -->|+ bruit| B[Légèrement bruité]
        B -->|+ bruit| C[Très bruité]
        C -->|+ bruit| D[Bruit pur]
    end
    subgraph Reverse [Enlever le bruit]
        E[Bruit pur] -->|- bruit| F[Forme visible]
        F -->|- bruit| G[Image nette]
    end
```

Imagine un puzzle qu'on défait pièce par pièce. Le modèle apprend à regarder n'importe quelle étape de ce démontage et à deviner quelle pièce remettre. Une fois ça maîtrisé, on part d'un bruit aléatoire et on applique le processus inverse, étape par étape, jusqu'à faire émerger une image cohérente. Si on conditionne ce processus sur un texte, on obtient les modèles text-to-image qu'on connaît.

**Avantages :** meilleure qualité d'image des trois, excellent contrôle par texte, entraînement stable.

**Limites :** lent à générer (des dizaines à des centaines d'étapes par image), coûteux en calcul.

**Exemples :** DALL-E 3, Midjourney, Stable Diffusion.

---

## Comparaison

| | GAN | VAE | Diffusion |
|---|---|---|---|
| **Idée centrale** | Compétition générateur / discriminateur | Compression et reconstruction | Apprendre à enlever le bruit |
| **Qualité d'image** | Très bonne | Correcte, parfois floue | Excellente |
| **Stabilité d'entraînement** | Difficile | Stable | Stable |
| **Vitesse de génération** | Rapide | Rapide | Lent |
| **Contrôle par texte** | Faible | Faible | Excellent |
| **Statut en 2026** | En recul | Recherche | Dominant |

Il n'y a pas de meilleure architecture dans l'absolu. Un GAN reste pertinent quand la vitesse compte plus que le contrôle. Un VAE est utile quand on a besoin d'explorer un espace latent structuré. Les modèles de diffusion dominent dès qu'on veut de la qualité et du guidage textuel.

---

## Pour finir

Ce qui est frappant avec ces trois architectures, c'est qu'elles ont toutes été développées dans la même décennie, et qu'elles convergent vers le même résultat — des machines capables de produire des images indiscernables du réel — en partant d'intuitions complètement différentes.

La question technique est en grande partie résolue. Ce qui reste ouvert, c'est la question de l'usage : ces outils n'ont pas d'intention propre — ils amplifient ce qu'on décide d'en faire.

---
