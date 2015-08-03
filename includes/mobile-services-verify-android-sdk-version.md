Poiché lo sviluppo è ancora in corso, la versione di Android SDK installata in Android Studio potrebbe non corrispondere a quella del codice. In questa esercitazione si fa riferimento alla versione 21 di Android SDK, ovvero la più recente al momento della stesura di questo documento. Il numero di versione dell'SDK può aumentare nel corso del tempo ed è consigliabile utilizzare la versione più recente disponibile.

Due sintomi di una mancata corrispondenza delle versioni sono i seguenti:

1. Quando si compila o ricompila il progetto, è possibile che vengano visualizzati messaggi di errore Gradle come "**impossibile trovare la destinazione Google Inc.:API Google:n**".

2. Gli oggetti Android standard nel codice che dovrebbero risolversi in base a istruzioni `import` potrebbero generare messaggi di errore.

Se si manifesta uno di questi sintomi, la versione di Android SDK installata in Android Studio potrebbe non corrispondere alla destinazione dell'SDK del progetto scaricato. Per verificare la versione, apportare le modifiche seguenti:


1. In Android Studio fare clic su **Tools** => **Android** => **SDK Manager**. Se la versione più recente della piattaforma SDK non è installata, fare clic per installarla. Prendere nota del numero di versione.

2. Nella scheda Project Explorer, in **Gradle Scripts**, aprire il file **build.gradle (modeule: app)**. Assicurarsi che i valori di **compileSdkVersion** e **buildToolsVersion** siano impostati sull'ultima versione dell'SDK installata. I tag potrebbero avere questo aspetto:
 
	 	    compileSdkVersion 'Google Inc.:Google APIs:21'
    		buildToolsVersion "21.1.2"
	
3. In Project Explorer di Android Studio fare clic con il pulsante destro del mouse sul nodo del progetto, scegliere **Properties** e fare clic su **Android** nella colonna a sinistra. Assicurarsi che il valore di **Project Build Target** sia impostato sulla stessa versione dell'SDK di **targetSdkVersion**.

4. A differenza di quanto avviene in Eclipse, in Android Studio il file manifesto non consente di specificare l'SDK di destinazione e la versione minima dell'SDK.

<!---HONumber=July15_HO4-->