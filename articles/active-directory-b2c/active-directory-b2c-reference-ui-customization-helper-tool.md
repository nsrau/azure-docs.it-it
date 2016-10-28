<properties
	pageTitle="Azure Active Directory B2C: strumento di supporto per la personalizzazione dell'interfaccia utente della pagina | Microsoft Azure"
	description="Strumento di supporto per la dimostrazione della funzionalità di personalizzazione dell'interfaccia utente della pagina in Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/22/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C: strumento di supporto per la dimostrazione della funzionalità di personalizzazione dell'interfaccia utente della pagina

Questo articolo è complementare all'[articolo principale sulla personalizzazione dell'interfaccia utente](active-directory-b2c-reference-ui-customization.md) in Azure Active Directory (Azure AD) B2C. I passaggi seguenti descrivono come verificare la funzionalità di personalizzazione dell'interfaccia utente delle pagine usando il contenuto HTML e CSS di esempio fornito.

## Ottenere un tenant di Azure AD B2C

Per poter eseguire la personalizzazione, è necessario prima [ottenere un tenant Azure Active Directory B2C](active-directory-b2c-get-started.md) qualora non sia ancora disponibile.

## Creare un criterio di iscrizione o accesso

Il contenuto di esempio reso disponibile può essere usato per personalizzare due pagine in [criteri iscrizione o accesso](active-directory-b2c-reference-policies.md): la [pagina di accesso unificata](active-directory-b2c-reference-ui-customization.md) e la [pagina di attributi autocertificati](active-directory-b2c-reference-ui-customization.md). Quando si [creano criteri di iscrizione o di accesso](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy), aggiungere l'account locale (indirizzo di posta elettronica), Facebook, Google e Microsoft come **provider di identità**. Questi sono gli unici provider di identità che verranno accettati dal contenuto HTML di esempio fornito. È anche possibile aggiungere un subset di questi provider di identità se necessario.

## Registrare un'applicazione

Sarà necessario [registrare un'applicazione](active-directory-b2c-app-registration.md) nel tenant B2C da usare per l'esecuzione del criterio. Dopo la registrazione dell'applicazione, sono disponibili alcune opzioni da usare per l'effettiva esecuzione dei criteri di iscrizione:

- Creare una delle applicazioni di avvio rapido di Azure AD B2C elencate nella sezione "Introduzione" dell'articolo [Iscrizione e accesso degli utenti alle applicazioni](active-directory-b2c-overview.md#getting-started).
- Usare l'applicazione esistente [Azure AD B2C Playground](https://aadb2cplayground.azurewebsites.net). In questo caso è necessario registrare un'applicazione nel tenant B2C usando l'**URI di reindirizzamento** `https://aadb2cplayground.azurewebsites.net/`.
- Usare il pulsante **Esegui adesso** nel criterio nel [portale di Azure](https://portal.azure.com/).

## Personalizzare il criterio

Per personalizzare l'aspetto dei criteri, è necessario prima creare file HTML e CSS usando le convenzioni specifiche di Azure AD B2C. È quindi possibile caricare il contenuto statico in un percorso disponibile pubblicamente in modo che Azure Active Directory B2C possa accedervi, ad esempio un server Web dedicato, un archivio BLOB di Azure, una rete per la distribuzione di contenuti di Azure o qualsiasi altro provider di servizi di hosting di risorse statiche. Come unici requisiti, è necessario che il contenuto sia disponibile su HTTPS e sia accessibile tramite CORS. Dopo aver esposto il contenuto statico sul Web, è possibile modificare il criterio in modo che punti a questo percorso e presenti il contenuto ai clienti. L'[articolo principale sulla personalizzazione dell'interfaccia utente](active-directory-b2c-reference-ui-customization.md) descrive in dettaglio il funzionamento della funzionalità di personalizzazione di Azure Active Directory B2C.

A fini di questa esercitazione, il contenuto di esempio è stato già creato ed è ospitato in Archiviazione BLOB di Azure. Si tratta di una personalizzazione di base del tema della società fittizia "Wingtip Toys". Per provarlo nel proprio criterio, seguire questa procedura:

1. Accedere al proprio tenant nel [portale di Azure](https://portal.azure.com/) e passare al pannello delle funzionalità B2C.
2. Fare clic su **Criteri di iscrizione o di accesso** e quindi sul criterio, ad esempio "b2c\_1\_sign\_up\_sign\_in".
3. Fare clic su **Personalizzazione dell'interfaccia utente della pagina** e quindi **Pagina unificata per l'iscrizione o l'accesso**.
4. Impostare l'opzione **Usa la pagina personalizzata** su **Sì**. Nel campo **URI pagina personalizzata** immettere `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`. Fare clic su **OK**.
5. Fare clic su **Pagina di iscrizione dell'account locale**. Impostare l'interruttore **Usa modello personalizzato** su **Sì**. Nel campo **URI pagina personalizzata** immettere `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`.
5. Ripetere lo stesso passaggio per **Pagina di iscrizione all'account di social networking**. Fare clic su **OK** due volte per chiudere i pannelli di personalizzazione dell'interfaccia utente.
6. Fare clic su **Save**.

È ora possibile provare il criterio personalizzato. È possibile usare la propria applicazione o Azure AD B2C Playground, se lo si vuole, oppure fare semplicemente clic sul comando **Esegui adesso** nel pannello dei criteri. Selezionare l'applicazione nella casella a discesa e scegliere l'URI di reindirizzamento appropriato. Fare clic sul pulsante **Esegui adesso**. Verrà visualizzata una nuova scheda del browser in cui è possibile eseguire l'esperienza utente di iscrizione per l'applicazione con il nuovo contenuto.

## Caricare il contenuto di esempio in Archiviazione BLOB di Azure

Se si desidera usare Archiviazione BLOB di Azure per ospitare il contenuto della pagina, è possibile creare il proprio account di archiviazione e usare lo strumento B2C helper per caricare i file.

### Creare un account di archiviazione

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **+ Nuovo** -> **Dati e archiviazione** -> **Account archiviazione**. Per creare un account di archiviazione BLOB di Azure è necessaria una sottoscrizione di Azure. È possibile registrarsi per una versione di valutazione gratuita nel [sito Web di Azure](https://azure.microsoft.com/pricing/free-trial/).
3. Specificare un valore in **Nome** per l'account di archiviazione, ad esempio "contoso", e selezionare le voci appropriate in **Piano tariffario**, **Gruppo di risorse** e **Sottoscrizione**. Verificare che l'opzione **Aggiungi alla Schermata iniziale** sia selezionata. Fare clic su **Create**.
4. Tornare alla schermata iniziale e fare clic sull'account di archiviazione appena creato.
5. Nella sezione **Riepilogo** fare clic su **Contenitori** e quindi su **+ Aggiungi**.
6. Specificare un valore in **Nome** per il contenitore, ad esempio "b2c", e quindi selezionare **BLOB** come **Tipo di accesso**. Fare clic su **OK**.
7. Il contenitore appena creato verrà visualizzato nell'elenco nel pannello **BLOB**. Prendere nota dell'URL del contenitore, che dovrebbe avere un aspetto simile al seguente: `https://contoso.blob.core.windows.net/b2c`. Chiudere il pannello **BLOB**.
8. Nel pannello dell'account di archiviazione fare clic su **Chiavi** e prendere nota dei valori dei campi **Nome account di archiviazione** e **Chiave di accesso primaria**.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **+ Nuovo** -> **Dati e archiviazione** -> **Account archiviazione**. Per creare un account di archiviazione BLOB di Azure è necessaria una sottoscrizione di Azure. È possibile registrarsi per una versione di valutazione gratuita nel [sito Web di Azure](https://azure.microsoft.com/pricing/free-trial/).
3. Selezionare **Archivio BLOB** in **Tipologia account** e lasciare gli altri valori predefiniti. Se necessario, è possibile modificare il gruppo di risorse e il percorso. Fare clic su **Crea**.
4. Tornare alla schermata iniziale e fare clic sull'account di archiviazione appena creato.
5. Nella sezione **Riepilogo** fare clic su **+Contenitore**.
6. Specificare un valore in **Nome** per il contenitore, ad esempio "b2c", e quindi selezionare **BLOB** come **Tipo di accesso**. Fare clic su **OK**.
7. Aprire il contenitore **Proprietà** e prendere nota dell'URL del contenitore. Dovrebbe avere un aspetto simile a `https://contoso.blob.core.windows.net/b2c`. Chiudere il pannello del contenitore.
8. Nel pannello dell'account di archiviazione fare clic sull'icona **Chiavi** e prendere nota dei valori dei campi **Nome account di archiviazione** e **Chiave di accesso primaria**.

> [AZURE.NOTE]
	La **Chiave di accesso primaria** è una credenziale di sicurezza importante.

### Scaricare lo strumento helper e i file di esempio

È possibile scaricare lo [strumento helper di archiviazione BLOB di Azure e i file di esempio come file con estensione zip qui](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) oppure clonarlo da GitHub:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Questo repository contiene una directory `sample_templates\wingtip`, che include file HTML, CSS e immagini di esempio. Affinché questi modelli facciano riferimento al proprio account di archiviazione BLOB di Azure, è necessario modificare i file HTML. Aprire `unified.html` e `selfasserted.html` e sostituire tutte le istanze di `https://localhost` con l'URL del proprio contenitore di cui si è preso nota nei passaggi precedenti. È necessario usare il percorso assoluto dei file HTML, perché in questo caso l'HTML verrà gestito da Azure AD, nel dominio `https://login.microsoftonline.com`.

### Caricare i file di esempio

Nello stesso repository decomprimere `B2CAzureStorageClient.zip` ed eseguire il file `B2CAzureStorageClient.exe` in esso contenuto. Questo programma caricherà semplicemente tutti i file nella directory specificata per l'account di archiviazione e abiliterà l'accesso CORS per tali file. Se è stata eseguita la procedura descritta precedentemente, i file HTML e CSS punteranno all'account di archiviazione. Si noti che il nome dell'account di archiviazione è la parte che precede `blob.core.windows.net`, ad esempio `contoso`. È possibile verificare che il contenuto sia stato caricato correttamente, provando ad accedere a `https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html` da un browser. Usare anche [http://test-cors.org/](http://test-cors.org/) per assicurarsi che il contenuto sia ora abilitato per la Condivisione di risorse tra origini (CORS) e cercare "XHR status: 200" nel risultato.

### Personalizzare di nuovo il criterio

Dopo aver caricato il contenuto di esempio nell'account di archiviazione, è necessario modificare il criterio di iscrizione in modo che faccia riferimento a tale contenuto. Ripetere i passaggi a partire dalla sezione ["Personalizzare il criterio"](#customize-your-policy) più indietro, questa volta usando gli URL del proprio account di archiviazione. Il percorso del file `unified.html` sarà ad esempio `<url-of-your-container>/wingtip/unified.html`.

È ora possibile usare il pulsante **Esegui adesso** o la propria applicazione per eseguire nuovamente il criterio. Il risultato dovrebbe essere lo stesso, poiché in entrambi i casi sono stati usati gli stessi file HTML e CSS di esempio. I criteri tuttavia fanno ora riferimento alla propria istanza di archiviazione BLOB di Azure e si è liberi di modificare e caricare di nuovo i file in base alle proprie esigenze. Per altre informazioni sulla personalizzazione dei file HTML e CSS, fare riferimento all'[articolo principale sulla personalizzazione dell'interfaccia utente](active-directory-b2c-reference-ui-customization.md).

<!---HONumber=AcomDC_0727_2016-->