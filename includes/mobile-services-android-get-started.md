La fase finale di questa esercitazione prevede la compilazione e l'esecuzione della nuova app.

### Caricare il progetto in Android Studio e sincronizzare Gradle
1. Individuare il percorso in cui sono stati salvati i file compressi del progetto ed espanderli nel computer in uso nella directory dei progetti di Android Studio.
2. Aprire Android Studio. Se viene visualizzato l'ultimo progetto al quale si è lavorato, chiuderlo (File = > Close Project).
3. Selezionare **Open an existing Android Studio project**, passare al percorso del progetto e quindi fare clic su **OK**. Il progetto verrà caricato e inizierà la sincronizzazione con Gradle.
   
     ![](./media/mobile-services-android-get-started/android-studio-import-project.png)
4. Attendere il completamento dell'attività di sincronizzazione con Gradle. Se viene visualizzato un errore che segnala che non è possibile trovare la destinazione, significa che la versione usata in Android Studio non corrisponde a quella dell'esempio. Il modo più semplice per risolvere il problema è fare clic sul collegamento **Install missing platform(s) and sync project** nel messaggio di errore. Se vengono visualizzati altri messaggi di errore relativi alla versione, ripetere semplicemente questo processo finché non verranno più restituiti errori.
   
   * Esiste un altro modo per risolvere il problema, se si vuole eseguire la versione migliore e più recente di Android. È possibile aggiornare **targetSdkVersion** nel file *build.gradle* nella directory *app* in modo che corrisponda alla versione già installata nel computer. Per conoscere tale versione, fare clic sull'icona **SDK Manager**. Premere quindi **Sync Project with Gradle Files**. Se viene visualizzato un messaggio di errore relativo alla versione di Build Tools, correggerlo nello stesso modo.

### Esecuzione dell'app
È possibile eseguire l'app con l'emulatore o con un dispositivo vero e proprio.

1. Per eseguirla da un dispositivo, connetterlo al computer con un cavo USB. È necessario [configurare il dispositivo per lo sviluppo](https://developer.android.com/training/basics/firstapp/running-app.html). Se si esegue lo sviluppo su un computer Windows, è anche necessario scaricare e installare un driver USB.
2. Per l'esecuzione con l'emulatore Android, è necessario definire almeno un dispositivo virtuale Android (AVD). Fare clic sull'icona AVD Manager per creare e gestire questi dispositivi.
3. Scegliere **Run** dal menu **Run** per avviare il progetto e scegliere un dispositivo o un emulatore nella finestra di dialogo visualizzata.
4. Quando viene visualizzata l'app, digitare un testo significativo, ad esempio *Complete the tutorial* e quindi fare clic su **Add**.
   
       ![](./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png)
   
       Verrà inviata una richiesta POST al nuovo servizio mobile ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal servizio mobile e i dati vengono visualizzati nell'elenco.
   
   > [!NOTE]
   > È possibile esaminare il codice che accede al servizio mobile per eseguire una query e inserire i dati, disponibile nel file ToDoActivity.java.
   > 
   > 
5. Nel portale di Azure classico fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItems**.
   
       ![](./media/mobile-services-android-get-started/mobile-data-tab1.png)
   
       In questo modo sarà possibile visualizzare i dati inseriti nella tabella dall'app.
   
       ![](./media/mobile-services-android-get-started/mobile-data-browse.png)

<!---HONumber=AcomDC_1203_2015-->