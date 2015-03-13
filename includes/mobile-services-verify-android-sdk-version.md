Poiché lo sviluppo è ancora in corso, è possibile che la versione di Android SDK installata in Eclipse non corrisponda a quella del codice. In questa esercitazione si fa riferimento alla versione 21 di Android SDK, ovvero la più recente al momento della stesura di questo documento. Il numero di versione dell'SDK può aumentare nel corso del tempo ed è consigliabile usare sempre la versione più recente disponibile.

Due sintomi di una mancata corrispondenza delle versioni sono i seguenti:

1. Esaminare la console di Eclipse nel riquadro inferiore. Potrebbero essere visualizzati messaggi di errore indicanti che **non è possibile risolvere la destinazione di tipo  'android-n'**.

2. Gli oggetti Android standard nel codice che dovrebbero risolversi in base a istruzioni `import` potrebbero generare messaggi di errore.

Se si manifesta uno di questi sintomi, la versione di Android SDK installata in Eclipse potrebbe non corrispondere alla destinazione dell'SDK del progetto scaricato.  Per verificare la versione, apportare le modifiche seguenti:


1. In Eclipse fare clic su **Window** e quindi su **Android SDK Manager**. Se non è stata installata la versione più recente della piattaforma SDK, fare clic per installarla. Prendere nota del numero di versione.

2. Aprire il file di progetto **AndroidManifest.xml**. Assicurarsi che nell'elemento **uses-sdk** il valore di **targetSdkVersion** sia impostato sulla versione più recente installata. Il tag **uses-sdk** potrebbe avere questo aspetto:
 
	 	    <uses-sdk
	 	        android:minSdkVersion="8"
	 	        android:targetSdkVersion="21" />
	
3. In Eclipse Package Explorer fare clic con il pulsante destro del mouse sul nodo del progetto, scegliere **Properties** e fare clic su **Android** nella colonna a sinistra. Assicurarsi che il valore di **Project Build Target** sia impostato sulla stessa versione dell'SDK di **targetSdkVersion**.
\<!--HONumber=42-->
