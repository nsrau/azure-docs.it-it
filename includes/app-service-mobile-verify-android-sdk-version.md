Poiché lo sviluppo è ancora in corso, la versione di Android SDK installata in Android Studio potrebbe non corrispondere a quella del codice. In questa esercitazione si fa riferimento alla versione 26 di Android SDK, ovvero la più recente al momento della stesura di questo documento. Il numero di versione dell'SDK può aumentare nel corso del tempo ed è consigliabile usare la versione più recente disponibile.

Due sintomi di una mancata corrispondenza delle versioni sono i seguenti:

- Quando si compila o ricompila il progetto, è possibile che vengano visualizzati messaggi di errore Gradle come `Gradle sync failed: Failed to find target with hash string 'android-XX'`.
- Gli oggetti Android standard nel codice che dovrebbero risolversi in base a istruzioni `import` potrebbero generare messaggi di errore.

Se si manifesta uno di questi sintomi, la versione di Android SDK installata in Android Studio potrebbe non corrispondere alla destinazione dell'SDK del progetto scaricato. Per verificare la versione, apportare le modifiche seguenti:

1. In Android Studio fare clic su **Tools** (Strumenti)  > **Android** > **SDK Manager**. Se la versione più recente della piattaforma SDK non è installata, fare clic per installarla. Prendere nota del numero di versione.

2. Nella scheda **Esplora progetti** in **Gradle Scripts** (Script Gradle) aprire il file **build.gradle (Module: app)**. Assicurarsi che i valori di **compileSdkVersion** e **targetSdkVersion** siano impostati sull'ultima versione dell'SDK installata. `build.gradle` potrebbe essere simile al seguente:

    ```gradle
    android {
        compileSdkVersion 26
        defaultConfig {
            targetSdkVersion 26
        }
    }
    ```
