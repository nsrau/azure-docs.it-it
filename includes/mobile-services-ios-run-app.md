La fase finale di questa esercitazione prevede la compilazione e l'esecuzione della nuova app.

1.  Individuare il percorso in cui sono stati salvati i file compressi del progetto ed espanderli nel computer in uso, quindi aprire il file di progetto tramite Xcode.

    ![][]

2.  Fare clic su **Esegui** per compilare il progetto e avviare l'applicazione nell'emulatore iPhone, che è l'impostazione predefinita per questo progetto.

3.  Nell'app digitare un testo significativo, ad esempio *Complete the tutorial*, quindi fare clic sull'icona con il segno PIÙ (**+**).

    ![][1]

    Verrà inviata una richiesta POST al nuovo servizio mobile ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal servizio mobile e i dati vengono visualizzati nell'elenco.

    > [WACOM.NOTE]È possibile esaminare il codice che accede al servizio mobile per eseguire una query e inserire i dati trovati nel file TodoService.m file.
    >
    > </div>
    > </p>

  []: ./media/mobile-services-ios-run-app/mobile-xcode-project.png
  [1]: ./media/mobile-services-ios-run-app/mobile-quickstart-startup-ios.png
