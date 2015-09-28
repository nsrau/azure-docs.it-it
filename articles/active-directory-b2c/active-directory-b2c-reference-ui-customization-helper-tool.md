<properties
	pageTitle="Anteprima di Azure Active Directory B2C: Strumento di supporto per la personalizzazione dell'interfaccia utente della pagina | Microsoft Azure"
	description="Strumento di supporto per la dimostrazione della funzionalità di personalizzazione dell'interfaccia utente della pagina in Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Anteprima di Azure Active Directory B2C: Strumento di supporto per la dimostrazione della funzionalità di personalizzazione dell'interfaccia utente della pagina

Questo articolo è complementare all'[articolo principale](active-directory-b2c-reference-ui-customization) che illustra la funzionalità di personalizzazione dell'interfaccia utente della pagina in Azure Active Directory B2C.

Le procedure seguenti consentono di esercitarsi con la funzionalità di personalizzazione dell'interfaccia utente della pagina usando il contenuto di esempio. Ai fini della dimostrazione, il contenuto di esempio è in formato HTML5 **statico**.

1. Caricare il contenuto di esempio nell'[archivio BLOB di Azure](http://azure.microsoft.com/documentation/services/storage/), renderlo pubblicamente accessibile tramite HTTPS e attivare la Condivisione di risorse tra le origini (CORS) su tali URL.
2. Modificare le impostazioni di personalizzazione dell'interfaccia utente della pagina nei criteri di iscrizione esistenti e specificare gli URL indicati in precedenza.

Prima di iniziare:

- Creare una delle applicazioni della guida introduttiva di Azure AD B2C elencate [qui](active-directory-b2c-overview.md). Come parte di questa procedura, sarà necessario creare i criteri di iscrizione che verranno poi modificati in questa procedura.
- Scaricare lo strumento di supporto da [qui](https://github.com/AzureADSamples/B2C-AzureBlobStorage-Client).

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

### Passaggio 1: Caricare il contenuto di esempio nelle posizioni corrette

1. Accedere al [portale di anteprima di Azure](https://portal.azure.com/).
2. Fare clic su **+ Nuovo** -> **Dati e archiviazione** -> **Account di archiviazione**. Per creare un account di archiviazione BLOB di Azure è necessaria una sottoscrizione di Azure. Fare clic [qui](https://azure.microsoft.com/it-IT/pricing/free-trial/) per usare la versione di valutazione gratuita.
3. Specificare il **Nome** dell'account di archiviazione, ad esempio "contoso.core.windows.net" e selezionare le voci appropriate in **Piano tariffario**, **Gruppo di risorse** e **Sottoscrizione**. Verificare che l'opzione **Aggiungi alla Schermata iniziale** sia selezionata. Fare clic su **Create**.
4. Tornare alla schermata iniziale e fare clic sull'account di archiviazione appena creato.
5. Nella sezione **Riepilogo** fare clic su **Contenitori** e quindi su **+ Aggiungi**.
6. Specificare un **Nome** per il contenitore, ad esempio "b2c", e quindi selezionare **BLOB** come **Tipo di accesso**. Fare clic su **OK**.
7. Il contenitore appena creato verrà visualizzato nell'elenco nel pannello **BLOB**. Prendere nota dell'URL del contenitore, che dovrebbe avere un aspetto simile al seguente: `https://contoso.blob.core.windows.net/b2c`. Chiudere il pannello **BLOB**.
8. Nel pannello dell'account di archiviazione fare clic su **Chiavi** e prendere nota dei valori dei campi **Nome account di archiviazione** e **Chiave di accesso primaria**.

    > [AZURE.NOTE]La **Chiave di accesso primaria** è una credenziale di sicurezza importante.

9. Eseguire lo strumento di supporto e specificare i valori di **Nome account di archiviazione** e **Chiave di accesso primaria** copiati nel passaggio precedente. In questo modo, il contenuto di esempio verrà caricato nell'account di archiviazione.
10. Per verificare che il contenuto sia stato caricato correttamente, provare ad accedere a `https://contoso.blob.core.windows.net/b2c/idp.html` da un browser. Usare anche [http://test-cors.org/](http://test-cors.org/) per assicurarsi che il contenuto sia ora abilitato per la Condivisione di risorse tra origini (CORS) e cercare `XHR status: 200` nel risultato.

### Passaggio 2: Modificare le opzioni di personalizzazione dell'interfaccia utente della pagina nei criteri di iscrizione

1. Accedere alla propria directory nel [portale di anteprima di Azure](https://portal.azure.com) e passare al pannello delle funzionalità B2C.
2. Fare clic su **Criteri di iscrizione** e quindi sul criterio stesso, ad esempio, "B2C\_1\_SiUp".
3. Fare clic su **Personalizzazione interfaccia utente della pagina** e quindi su **Pagina di selezione del provider di identità**.
4. Impostare l'interruttore **Usa modello personalizzato** su **Sì**. Nel campo **URI pagina personalizzata** specificare l'URL appropriato corrispondente al contenuto caricato nell'account di archiviazione, ad esempio `https://contoso.blob.core.windows.net/b2c/idp.html`. Fare clic su **OK**.
5. Fare clic su **Pagina di iscrizione dell'account locale**. Impostare l'interruttore **Usa modello personalizzato** su **Sì**. Nel campo **URI pagina personalizzata** specificare l'URL appropriato corrispondente al contenuto caricato nell'account di archiviazione. Ad esempio: `https://contoso.blob.core.windows.net/b2c/su.html`. Fare clic su **OK** due volte.
6. Fare clic su **Save**.
7. Fare clic sul comando **Esegui adesso** nella parte superiore del pannello. Selezionare "B2C app" nel menu a discesa **Applicazioni** e `https://localhost:44321/` nel menu a discesa **URL di risposta / URI di reindirizzamento**. Fare clic sul pulsante **Esegui adesso**.
8. Verrà visualizzata una nuova scheda del browser in cui è possibile eseguire l'esperienza utente di iscrizione per l'applicazione con il nuovo contenuto.

> [AZURE.NOTE]Si noti che le modifiche apportate ai criteri avranno effetto dopo circa un minuto.

<!---HONumber=Sept15_HO3-->