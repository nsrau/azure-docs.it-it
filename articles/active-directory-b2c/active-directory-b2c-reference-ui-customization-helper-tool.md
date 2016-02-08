<properties
	pageTitle="Anteprima di Azure Active Directory B2C: Strumento di supporto per la personalizzazione dell'interfaccia utente della pagina | Microsoft Azure"
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
	ms.date="12/22/2015"
	ms.author="swkrish"/>

# Anteprima di Azure Active Directory B2C: Strumento di supporto per la dimostrazione della funzionalità di personalizzazione dell'interfaccia utente della pagina

Questo articolo è complementare all'[articolo principale sulla personalizzazione dell'interfaccia utente](active-directory-b2c-reference-ui-customization.md) in Azure Active Directory B2C. I passaggi seguenti descrivono come verificare la funzionalità di personalizzazione dell'interfaccia utente delle pagine usando il contenuto HTML e CSS di esempio fornito.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Ottenere un tenant di Azure AD B2C

Per poter eseguire la personalizzazione, è necessario prima [ottenere un tenant Azure Active Directory B2C](active-directory-b2c-get-started.md) qualora non sia ancora disponibile.

## Creare un criterio di iscrizione

Il contenuto di esempio fornito personalizza due pagine in un [criterio di personalizzazione](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy): la [pagina di selezione dei provider di identità](active-directory-b2c-reference-ui-customization.md#identity-provider-selection-page) e la [pagina di iscrizione dell'account locale](active-directory-b2c-reference-ui-customization.md#local-account-sign-up-page). Quando si [crea il criterio di iscrizione](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy), aggiungere l'account locale (indirizzo di posta elettronica), Facebook e Google+ come **provider di identità**. Questi sono gli unici provider di identità che verranno accettati dal contenuto HTML di esempio fornito.

## Registrare un'applicazione

Sarà necessario [registrare un'applicazione](active-directory-b2c-app-registration.md) nel tenant B2C da usare per l'esecuzione del criterio. Dopo la registrazione dell'applicazione, sono disponibili alcune opzioni che è possibile usare per l'effettiva esecuzione del criterio di iscrizione:

- Creare una delle applicazioni di avvio rapido di Azure Active Directory B2C elencate [qui](active-directory-b2c-overview.md#getting-started).
- Usare l'applicazione esistente [Azure AD B2C Playground](https://aadb2cplayground.azurewebsites.net). In questo caso, è necessario registrare un'applicazione nel tenant B2C usando l'**URI di reindirizzamento** `https://aadb2cplayground.azurewebsites.net/`
- Usare il pulsante **Esegui adesso** nel criterio nel [portale di Azure](https://portal.azure.com/).

## Personalizzare il criterio

Per personalizzare l'aspetto dei criteri, è necessario prima creare file HTML e CSS usando le convenzioni specifiche di Azure Active Directory B2C. È quindi possibile caricare il contenuto statico in un percorso disponibile pubblicamente in modo che Azure Active Directory B2C possa accedervi, ad esempio un server Web dedicato, Archiviazione BLOB di Azure, la rete CDN di Azure o qualsiasi altro provider di servizi di hosting di risorse statiche. Come unici requisiti è necessario che il contenuto sia disponibile su HTTPS e sia accessibile tramite CORS. Dopo aver esposto il contenuto statico sul Web, è possibile modificare il criterio in modo che punti a questo percorso e presenti il contenuto agli utenti finali. L'[articolo principale sulla personalizzazione dell'interfaccia utente](active-directory-b2c-reference-ui-customization.md) descrive in dettaglio il funzionamento della funzionalità di personalizzazione di Azure Active Directory B2C.

A fini di questa esercitazione, il contenuto di esempio è stato già creato ed è ospitato in Archiviazione BLOB di Azure. Si tratta di una personalizzazione di base del tema della società fittizia "Contoso B2C". Per provarlo nel proprio criterio, seguire questa procedura:

1. Accedere al proprio tenant nel [portale di Azure](https://portal.azure.com/) e passare al pannello delle funzionalità B2C.
2. Fare clic su **Criteri di iscrizione** e quindi sul criterio stesso, ad esempio, "b2c\_1\_sign\_up".
3. Fare clic su **Personalizzazione interfaccia utente della pagina** e quindi su **Pagina di selezione del provider di identità**.
4. Impostare l'interruttore **Usa modello personalizzato** su **Sì**. Nel campo **URI pagina personalizzata** immettere `https://contosob2c.blob.core.windows.net/static/Index.html`. Fare clic su **OK**.
5. Fare clic su **Pagina di iscrizione dell'account locale**. Impostare l'interruttore **Usa modello personalizzato** su **Sì**. Nel campo **URI pagina personalizzata** immettere `https://contosob2c.blob.core.windows.net/static/EmailVerification.html`. Fare clic su **OK** due volte per chiudere i pannelli di personalizzazione dell'interfaccia utente.
6. Fare clic su **Save**.

È ora possibile provare il criterio personalizzato. È possibile usare la propria applicazione oppure AAD B2C Playground, se lo si desidera, ma è anche possibile fare semplicemente clic sul comando **Esegui adesso** nel pannello del criterio. Selezionare l'applicazione nell'elenco a discesa e l'URI di reindirizzamento appropriato. Fare clic sul pulsante **Esegui adesso**. Verrà visualizzata una nuova scheda del browser in cui è possibile eseguire l'esperienza utente di iscrizione per l'applicazione con il nuovo contenuto.

## Caricare il contenuto di esempio in Archiviazione BLOB di Azure

Se si desidera usare Archiviazione BLOB di Azure per ospitare il contenuto della pagina, è possibile creare il proprio account di archiviazione e usare lo strumento B2C helper per caricare i file.

#### Creare un account di archiviazione

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **+ Nuovo** -> **Dati e archiviazione** -> **Account di archiviazione**. Per creare un account di archiviazione BLOB di Azure è necessaria una sottoscrizione di Azure. Fare clic [qui](https://azure.microsoft.com/pricing/free-trial/) per usare la versione di valutazione gratuita.
3. Specificare un valore in **Nome** per l'account di archiviazione, ad esempio "contoso", e selezionare le voci appropriate in **Piano tariffario**, **Gruppo di risorse** e **Sottoscrizione**. Verificare che l'opzione **Aggiungi alla Schermata iniziale** sia selezionata. Fare clic su **Create**.
4. Tornare alla schermata iniziale e fare clic sull'account di archiviazione appena creato.
5. Nella sezione **Riepilogo** fare clic su **Contenitori** e quindi su **Aggiungi**.
6. Specificare un valore in **Nome** per il contenitore, ad esempio "b2c", e quindi selezionare **BLOB** come **Tipo di accesso**. Fare clic su **OK**.
7. Il contenitore appena creato verrà visualizzato nell'elenco nel pannello **BLOB**. Prendere nota dell'URL del contenitore, che dovrebbe avere un aspetto simile al seguente: `https://contoso.blob.core.windows.net/b2c`. Chiudere il pannello **BLOB**.
8. Nel pannello dell'account di archiviazione fare clic su **Chiavi** e prendere nota dei valori dei campi **Nome account di archiviazione** e **Chiave di accesso primaria**.

> [AZURE.NOTE]
	La **Chiave di accesso primaria** è una credenziale di sicurezza importante.

#### Scaricare lo strumento helper e i file di esempio

È possibile scaricare lo [strumento helper Archiviazione BLOB di Azure e i file di esempio come file con estensione zip qui](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) oppure clonarlo da GitHub:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Questo repository contiene una directory `sample_templates\contoso`, che include file HTML, CSS e immagini di esempio. Affinché questi modelli facciano riferimento al proprio account di Archiviazione BLOB di Azure, è necessario modificare i file HTML. Aprire `Index.htnml` e `EmailValidation.html` e sostituire tutte le istanze di `https://localhost` con l'URL del proprio contenitore copiato nei passaggi precedenti. È necessario usare il percorso assoluto dei file HTML perché in questo caso l'HTML verrà gestito da Azure Active Directory, nel dominio `https://login.microsoftonline.com`.

#### Caricare i file di esempio

Nello stesso repository decomprimere `B2CAzureStorageClient.zip` ed eseguire il file `B2CAzureStorageClient.exe` in esso contenuto. Questo programma caricherà semplicemente tutti i file nella directory specificata per l'account di archiviazione e abiliterà l'accesso CORS per tali file. Se è stata eseguita la procedura descritta precedentemente, i file HTML e CSS punteranno all'account di archiviazione. Si noti che il nome dell'account di archiviazione è la parte che precede `blob.core.windows.net`, ad esempio `contoso`. È possibile verificare che il contenuto sia stato caricato correttamente, provando ad accedere a `https://{storage-account-name}.blob.core.windows.net/{container-name}/Index.html` da un browser. Usare anche [http://test-cors.org/](http://test-cors.org/) per assicurarsi che il contenuto sia ora abilitato per la Condivisione di risorse tra origini (CORS) e cercare lo status XHR: 200 nel risultato.

#### Personalizzare di nuovo il criterio

Dopo aver caricato il contenuto di esempio nell'account di archiviazione, è necessario modificare il criterio di iscrizione in modo che faccia riferimento a tale contenuto. Ripetere i passaggi a partire dalla sezione ["Personalizzare il criterio"](#customize-your-policy) più indietro, questa volta usando gli URL del proprio account di archiviazione. Il percorso del file `Index.html` sarà ad esempio `<url-of-your-container>/Index.html`.
        
È ora possibile usare il pulsante **Esegui adesso** o la propria applicazione per eseguire nuovamente il criterio. Il risultato dovrebbe essere lo stesso, poiché in entrambi i casi sono stati usati gli stessi file HTML e CSS di esempio. I criteri tuttavia fanno ora riferimento alla propria istanza di Archiviazione BLOB di Azure e si è liberi di modificare e caricare di nuovo i file in base alle proprie esigenze. Per altre informazioni sulla personalizzazione dei file HTML e CSS, fare riferimento all'[articolo principale sulla personalizzazione dell'interfaccia utente](active-directory-b2c-reference-ui-customization.md).

<!---HONumber=AcomDC_0128_2016-->