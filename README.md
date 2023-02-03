# Application Notes Avec Kotlin et Jetpack Compose

Pendant ce workshop vous allez découvrir comment marche Jetpack Compose.


## 1re Étape:

Pour commencer, vous allez devoir créer un nouveau projet sur Android Studio, vous devez créer un "Empty Compose Activity".
![Step1](https://raw.githubusercontent.com/EnzoP99/Epitech_WorkShop_Kotlin_Memo_App/main/Screen/1.png)
![Step2](https://raw.githubusercontent.com/EnzoP99/Epitech_WorkShop_Kotlin_Memo_App/main/Screen/2.png)

Maintenant, que vous avez créé votre nouveau projet, on va pouvoir commencer.

## 2e Étape:
Nous allons d'abord commencer par créé une ["DATA CLASS"](https://kotlinlang.org/docs/data-classes.html) qui va-nous permettre de stocker à l'intérieur les infos de chaque mémo.

Vous devez créer un fichier Kotlin qu'on va appeler NoteItem et nous allons mettre notre "data class" à l'intérieur

```kotlin
// Une Data class commence toujours par une majuscule.

data class NoteItem(
    var title: String, // Titre de la Note
    var text: String // Texte de la Note
)
```

Ensuite, nous allons créer un viewModel pour stocker la list de Note.

```kotlin
class MyViewModel: ViewModel() {
    private val _notes = MutableLiveData<List<NoteItem>>(emptyList())
    // Private, car il faut la modifier en passant par les fonctions en dessous.

    val note: LiveData<List<NoteItem>>
        get() = _notes
    // Permet de get la liste de note

    fun setNotes(items: List<NoteItem>) {
        _notes.value = items
    }// Permet de set la liste de notes

    fun addNote(newNote: NoteItem) {
        //_notes.value!!.add(newNote)
        val currentList = _notes.value ?: emptyList()
        _notes.value = currentList + newNote
    }// Permet d'ajouter une nouvelle note dans la liste

    fun removeNote(note: NoteItem) {
        if (_notes.value != null) {
            val currentList = _notes.value!!.toMutableList()
            currentList.remove(note)
            _notes.value = currentList.toList()
        }
    }// Permet de supprimer une note de la liste*/
}
```

Vous allez devoir implémenter des libraires pour cela, vous allez devoir vous rendre dans le dossier Gradle Scripts > build.gradle ajouté les lignes suivantes.
```kotlin
dependencies {
    // Les libraires de base
    ...
    implementation "androidx.compose.material:material-icons-extended:1.3.1"
    // Ceci nous servira pour une prochaine étape.
    implementation "androidx.navigation:navigation-compose:2.5.3"
    // Ceci nous servira pour une prochaine étape.
    
    implementation "androidx.compose.runtime:runtime-livedata:1.4.0-alpha04"
}
```

Maintenant, que nous avons notre data class et notre viewModel pour stocker les informations d'une Note et les notes, nous allons pouvoir commencer à créer la vue.

## 3e Étape:

Nous allons maintenant créer notre première vue celle qui nous permettra de modifier la note.

- Pour cela, nous allons créer un nouveau fichier Kotlin qui s'appellera NoteView.
- Maintenant, nous allons créer une fonction Composable ce qui va nous permettre de créer une vue. Avec Jetpack Compose les vues commence toujours avec l'annotations @Composable au-dessus de la fonction.

```kotlin
/*
 Une fonction Composable commence
 toujours avec une majuscule.
*/

@Composable
fun NoteView() {
    
}

```

- Nous allons créer à l'intérieur une "Column" qui va nous permettre de ranger les Composables de façon verticale les uns en dessous des autres.

```kotlin
/*
    Le modifier nous permets de modifier les layout,
    ici par exemple, on lui dit de prendre toute la
    taille possible sur l'écran et de mettre un fond gris.
*/
Column (
    modifier = Modifier
        .fillMaxSize()
        .background(Color.DarkGray)
) {
    /*
        Vous allez devoir mettre un OutlinedTextField() qui va vous permettre de modifier
        votre titre qui prend toute la largeur du layout parent et 50.dp de hauteur
        avec un background de Color.Gray
        Ensuite, vous allez devoir mettre un autre OutlinedTextField() qui vous permettra
        de modifier le texte, le OutlinedTextField() prendra toute la largeur du layout parent
    */
}
```


Votre but et d'avoir à la fin quelque chose qui ressemble a ceci:
![Step3](https://raw.githubusercontent.com/EnzoP99/Epitech_WorkShop_Kotlin_Memo_App/main/Screen/3.png)


Maintenant, que nous avons fini de faire la vue, nous allons devoir modifier la data class pour que les valeurs s'actualisent automatiquement dans la vue quand une valeur est modifié.

Pour l'instant notre data class ressemble à ceci:
```kotlin
data class NoteItem(
    var title: String,
    var text: String
)
```

Pour que les valeurs s'actualisent dans la vue quand elles sont modifiées, il faut utiliser des MutableStateFlow ce qui va donner ceci :
```kotlin
data class NoteItem(
    var title: MutableStateFlow<String> = MutableStateFlow(""),
    var text: MutableStateFlow<String> = MutableStateFlow("")
)

/*
    = MutableStateFlow("") Signifie que si vous ne donnez pas de valeur à la création
    de la data class la valeur par défaut sera MutableStateFlow("")
*/
```

Pour utiliser les MutableStateFlow il vous suffit de rajouter .value après la valeur exemple:
```kotlin
var title: MutableStateFlow<String> = MutableStateFlow("")

println(title.value)
```

ou  si vous utiliser la valeur dans un composable il faut juste ajouter .collectAsState() exemple:

```kotlin
var title: MutableStateFlow<String> = MutableStateFlow("")

OutlinedTextField(
    value = title.collectAsState().value,
    onValueChange = {
        title.value = it
        /*
        Ici, on n'ajoute pas .collectAsState(), car onValueChange est une fonction 
        et non un fonction composable 
         */
    },
    modifier = Modifier
        .fillMaxWidth(),
)
```

Maintenant, il va falloir que nous envoyons à notre vue notre data class pour finir la vue, pour cela, nous allons rajouter en argument de notre vue "item : NoteItem"

```kotlin
@Composable
fun NoteView(item: NoteItem) {
    ...
}
```

Normalement votre vue devrez ressembler a ceci:

```kotlin
@Composable
fun NoteView(item: NoteItem) {
    Column (
        modifier = Modifier
            .fillMaxSize()
            .background(Color.DarkGray)
    ) {
        OutlinedTextField(
            value = // Ici mettre le titre,,
            onValueChange = {
                // Ici mettre la modification du titre
            },
            modifier = Modifier
                .fillMaxWidth()
                .height(50.dp)
                .background(Color.Gray),
            maxLines = 1,
            singleLine = true,
        )
        OutlinedTextField(
            value = // Ici mettre le text,
            onValueChange = {
                // Ici mettre la modification du text
            },
            modifier = Modifier
                .fillMaxWidth(),
        )
    }
}
```

## 4e Étape:

Maintenant, que nous avons la vue d'une note, nous allons réaliser la vue avec toutes les notes.

Donc, pour cela, nous allons recréer un nouveau fichier qu'on va appeler HomeView et créer une fonction composable qui s'appellera HomeView, qui prendra en argument une MutableList de NoteItem.

```kotlin
@Composable
fun HomeView(notes: MutableList<NoteItem>) {

}
```

Pour afficher toutes les notes de les listes, on va utiliser une LazyColumn

```kotlin
@Composable
fun HomeView(notes: MutableList<NoteItem>) {
    val note = viewModel.note.observeAsState().value ?: emptyList()

    Column(
        modifier = Modifier
            .fillMaxSize()
            .background(Color.DarkGray)
    ) {
        // Ici mettre un en-tête avec un bouton pour ajouter une nouvelle note
        LazyColumn(
            modifier = Modifier
                .fillMaxSize()
        ) {
            items(items = notes) {note -> // Ici, il vous suffit de donner à items la liste.
                /*
                    Ici, on va mettre Row avec une Icon cliquable pour supprimer la 
                    note de la liste et un Text Cliquable qui prend toute la largeur
                    de l'écran, qui a en hauteur 50.dp, qui a pour background Color.Gray,
                    et qui affichera le titre de la note.
                    Et on va mettre un Spacer() en dessous qui prend toute la largeur de
                    l'écran, qui a en hauteur 2.dp, qui a pour background Color.Black,
                    comme ça, cela va permettre de mettre une barre noire qui séparera
                    les notes.
                */
            }
        }
    }
}
```

Ce qui vous donnera ceci :

![Step4](https://raw.githubusercontent.com/EnzoP99/Epitech_WorkShop_Kotlin_Memo_App/main/Screen/4.png)


## 5e Étape:

Maintenant, on va connecter nos deux vues ensemble.

On va commencer par créer un fichier Navigation, avec une fonction composable Navigation à l'intérieur


```kotlin
sealed class Routes(val title: String) {
    object HomeScreen : Routes("HomeScreen")
    object NoteScreen : Routes("NoteScreen")
}
/*
    La class Routes nous permets de créer des routes et nous permets
    de connecter les vues entre elles
 */

@Composable
fun Navigation(
    navController: NavHostController,
    viewModel: MyViewModel
) {
    NavHost(
        navController,
        startDestination = Routes.HomeScreen.title
    ) {
        /*
            Ici, nous allons utiliser la class Route et set des navigation link
            grâce à NavHost et à la fonction composable().
         */
        /*

            composable(Routes.NomDeVotreView.title) {
                Votre fonction composable donc votre vue
            }

        */

        composable(Routes.HomeScreen.title) {
            HomeView(navController, viewModel)
        }// Ceci est le composable Basic

        composable(
            Routes.NoteScreen.title + "/{noteIndex}",
            arguments = listOf(navArgument("noteIndex") { type = NavType.StringType })
        ) { navBackStackEntry ->

            val noteIndex: String? = navBackStackEntry.arguments?.getString("noteIndex")
            noteIndex?.let {
                NoteView(noteIndex.toInt(), viewModel)
            }
        }
        /*
        Ceci est la fonction composable qui peut être utilisé avec un argument pour
        créer une vue qui change en fonction d'une valeur, par exemple en fonction
        de l'index envoyé, notre vue ne montrera pas la même note.
         */
    }
}
```

## 6e Étape:

Pour la dernière étape, nous allons juste implémenter dans MainActivity notre Composable de navigation.

Pour cela, il vous suffit juste d'initialiser un navController et d'appeler votre composable de navigation.
```kotlin
val navController = rememberNavController()
Navigation(navController, viewModel)
```

Ensuite, il vous reste juste à implémenter le viewModel au-dessus de la fonction onCreate()
```kotlin
 private val noteViewModel by viewModels<MyViewModel>()
 /* 
    by viewModels<MyViewModel>() signifie que la valeur de la propriété sera déléguée
    à une instance d'un objet de délégation. Dans notre cas, cela permet de déléguer
    la responsabilité de la définition de notre MyViewModel à viewModels.
 */
```

Ce qui devrait vous donner ceci:
```kotlin
class MainActivity : ComponentActivity() {
    private val noteViewModel by viewModels<MyViewModel>()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            NoteAPPTheme {
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colors.background
                ) {
                    val navController = rememberNavController()
                    Navigation(navController, noteViewModel)
                }
            }
        }
    }
}
```
