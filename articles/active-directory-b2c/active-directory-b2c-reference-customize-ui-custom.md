---
title: 'Azure Active Directory B2C: informazioni di riferimento: personalizzare l''interfaccia utente di un percorso utente con i criteri personalizzati | Microsoft Docs'
description: Un argomento sui criteri personalizzati di Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: mtillman
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/25/2017
ms.author: joroja
ms.openlocfilehash: f07e05a1786f8afc1ccecf21994b1ad263ead285
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>Personalizzare l'interfaccia utente di un percorso utente con criteri personalizzati

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> Questo articolo descrive in modo approfondito come funziona la personalizzazione dell'interfaccia utente e come abilitarla con i criteri personalizzati B2C, usando il framework di esperienza di gestione delle identità


Un'esperienza utente integrata è fondamentale per qualsiasi soluzione Business to Consumer. Per esperienza utente integrata si intende un'esperienza, con un dispositivo o un browser, in cui il percorso di un utente nel servizio non sia distinguibile dal quello del servizio clienti usato.

## <a name="understand-the-cors-way-for-ui-customization"></a>Informazioni sulla modalità CORS per la personalizzazione dell'interfaccia utente

Azure AD B2C consente di personalizzare l'aspetto dell'esperienza utente nelle diverse pagine che possono essere potenzialmente rese disponibili e visualizzate da Azure AD B2C tramite i criteri personalizzati.

A tale scopo, Azure AD B2C esegue il codice nel browser del cliente e usa il moderno approccio standard [Condivisione risorse tra le origini (CORS)](http://www.w3.org/TR/cors/) per caricare il contenuto personalizzato da un determinato URL che punta ai modelli HTML5/CSS, come si specifica in un criterio personalizzato. CORS è un meccanismo che consente alle risorse limitate, ad esempio i tipi di carattere, in una pagina Web di essere richieste da un altro dominio esterno al dominio da cui la risorsa è stata originata.

Rispetto alla precedente modalità tradizionale, in cui le pagine modello sono di proprietà della soluzione, nella quale si inserivano testo e immagini limitati e il controllo del layout e dell'aspetto era limitato al punto che non era semplice raggiungere un'esperienza integrata, la modalità CORS supporta HTML5 e CSS e consente di:

- Ospitare il contenuto e inserire nella soluzione i controlli usando lo script lato client.
- Avere il controllo completo di ogni pixel del layout e dell'aspetto.

È possibile inserire un numero illimitato di pagine contenuto creando file HTML5/CSS secondo le esigenze.

> [!NOTE]
> Per motivi di sicurezza non è attualmente possibile usare JavaScript per la personalizzazione. Per sbloccare JavaScript, è necessario usare un nome di dominio per il tenant Azure AD B2C.

In ogni modello HTML5/CSS si inserisce un elemento *anchor*, che corrisponde all'elemento `<div id=”api”>` necessario nella pagina HTML o contenuto, come illustrato più avanti. Azure AD B2C richiede che tutte le pagine contenuto abbiano questo specifico elemento div.

```
<!DOCTYPE html>
<html>
  <head>
    <title>Your page content’s tile!</title>
  </head>
  <body>
    <div id="api"></div>
  </body>
</html>
```

Il contenuto relativo ad Azure AD B2C per la pagina verrà inserito in questo div, mentre il resto della pagina può essere controllato dall'utente. Il codice JavaScript di Azure AD B2C effettua il pull del contenuto e inserisce il codice HTML in questo specifico elemento div. Azure AD B2C inserisce i controlli seguenti secondo le esigenze: controllo di scelta account, controlli di accesso, controlli a più fattori (attualmente basati sul telefono) e controlli di raccolta di attributi. Azure AD B2C assicura che tutti i controlli siano conformi a HTML5 e accessibili, che tutti i controlli possano essere completamente personalizzati con stili e che la versione di un controllo non regredisca.

Il contenuto unito viene infine visualizzato dal cliente come documento dinamico.

Per assicurarsi che tutto funzioni come previsto, è necessario:

- Assicurarsi che il contenuto sia conforme a HTML5 e accessibile
- Assicurarsi che il server di contenuti sia abilitato per CORS.
- Rendere disponibile il contenuto tramite HTTPS.
- Usare URL assoluti, ad esempio https://yourdomain/content per tutti i collegamenti e il contenuto CSS.

> [!TIP]
> Per verificare che CORS sia abilitato per il sito su cui si ospita il contenuto e per testare le richieste CORS, è possibile usare il sito http://test-cors.org/. Da questo sito è semplicemente possibile inviare la richiesta CORS a un server remoto (per verificare se CORS è supportato) oppure a un server di test (per esplorare determinate funzionalità di CORS).

> [!TIP]
> Il sito http://enable-cors.org/ costituisce anche una risorsa più che utile per CORS.

Grazie a questo approccio basato su CORS, gli utenti finali avranno quindi esperienze coerenti nell'applicazione e nelle pagine rese disponibili da Azure AD B2C.

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Come prerequisito, è necessario creare un account di archiviazione. Per creare un account di archiviazione BLOB di Azure è necessaria una sottoscrizione di Azure. È possibile registrarsi per una versione di valutazione gratuita nel [sito Web di Azure](https://azure.microsoft.com/en-us/pricing/free-trial/).

1. Aprire una sessione del browser e passare al [portale di Azure](https://portal.azure.com).
2. Accedere con le credenziali amministrative.
3. Fare clic su **Nuovo** > **Dati e archiviazione** > **Account di archiviazione**.  Viene aperto un pannello **Crea account di archiviazione**.
4. In **Nome** specificare un nome per l'account di archiviazione, ad esempio *contoso369b2c*. Questo valore più avanti verrà indicato come *storageAccountName*.
5. Impostare le selezioni appropriate per il piano tariffario, il gruppo di risorse e la sottoscrizione. Verificare che l'opzione **Aggiungi alla Schermata iniziale** sia selezionata. Fare clic su **Crea**.
6. Tornare alla schermata iniziale e fare clic sull'account di archiviazione appena creato.
7. Nella sezione **Servizi** fare clic su **BLOB**. Viene aperto un pannello **Servizio BLOB**.
8. Fare clic su **+ Contenitore**.
9. In **Nome** specificare un nome per il contenitore, ad esempio *b2c*. Questo valore più avanti verrà indicato come *containerName*.
9. Selezionare **BLOB** come **Tipo di accesso**. Fare clic su **Crea**.
10. Il contenitore appena creato verrà visualizzato nell'elenco nel pannello  **Servizio BLOB**.
11. Chiudere il pannello **BLOB** .
12. Nel pannello **Account di archiviazione** fare clic sull'icona **Chiave**. Viene aperto un pannello **Chiavi di accesso**.  
13. Annotare il valore di **key1**. Questo valore più avanti verrà indicato come *key1*.

## <a name="downloading-the-helper-tool"></a>Download dello strumento di supporto

1.  [Scaricare lo strumento di supporto da ](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip)GitHub.
2.  Salvare il file *B2C-AzureBlobStorage-Client-master.zip* nel computer locale.
3.  Estrarre il contenuto del file B2C-AzureBlobStorage-Client-master.zip nel disco locale, ad esempio nella cartella **UI-Customization-Pack**. Verrà creata una cartella *B2C-AzureBlobStorage-Client-master*.
4.  Aprire tale cartella ed estrarre il contenuto del file di archivio *B2CAzureStorageClient.zip*.

## <a name="upload-the-ui-customization-pack-sample-files"></a>Caricare i file di esempio di UI-Customization-Pack

1.  Usando Esplora risorse, passare alla cartella *B2C-AzureBlobStorage-Client-master* nella cartella *UI-Customization-Pack* creata nella sezione precedente.
2.  Eseguire il file *B2CAzureStorageClient.exe*. Questo programma caricherà semplicemente tutti i file nella directory specificata per l'account di archiviazione e abiliterà l'accesso CORS per tali file.
3.  Quando richiesto, specificare: a.  Il nome dell'account di archiviazione, *storageAccountName*, ad esempio *contoso369b2c*.
    b.  La chiave di accesso primaria dell'archivio BLOB di Azure, *key1*, ad esempio *contoso369b2c*.
    c.  Il nome del contenitore dell'archivio BLOB del servizio di archiviazione, *containerName*, ad esempio *b2c*.
    d.  Il percorso dei file di esempio dello *starter pack*, ad esempio *..\B2CTemplates\wingtiptoys*.

Se la procedura precedente è stata eseguita, i file HTML5 e CSS di *UI-Customization-Pack* per la società fittizia **wingtiptoys** punteranno così all'account di archiviazione.  Per verificare che il contenuto sia stato caricato correttamente, aprire il pannello del contenitore correlato nel portale di Azure. In alternativa, per verificare che il contenuto sia stato caricato correttamente, accedere alla pagina da un browser. Per altre informazioni, vedere [Azure Active Directory B2C: strumento di supporto per la dimostrazione della funzionalità di personalizzazione dell'interfaccia utente della pagina](active-directory-b2c-reference-ui-customization-helper-tool.md).

## <a name="ensure-the-storage-account-has-cors-enabled"></a>Assicurarsi che CORS sia abilitato per l'account di archiviazione

La condivisione di risorse tra le origini (CORS) deve essere abilitata nell'endpoint in modo che Azure AD B2C Premium possa caricare il contenuto. Infatti il contenuto è ospitato in un dominio diverso dal dominio da cui Azure AD B2C Premium renderà disponibile la pagina.

Per verificare che CORS sia abilitato per la risorsa di archiviazione in cui si ospita il contenuto, procedere con i passaggi seguenti:

1. Aprire una sessione del browser e passare alla pagina *unified.html* usando l'URL completo del percorso nell'account di archiviazione, `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html`. Ad esempio, https://contoso369b2c.blob.core.windows.net/b2c/unified.html.
2. Passare a http://test-cors.org. Questo sito consente di verificare che CORS sia abilitato per la pagina in uso.  
<!--
![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
-->

3. In **Remote URL** (URL remoto) immettere l'URL completo per il contenuto di unified.html e fare clic su **Send Request** (Invia richiesta).
4. Verificare che l'output nella sezione **Results** (Risultati) contenga *XHR status: 200* (Stato XHR: 200). Questa impostazione indica che CORS è abilitato.
<!--
![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
-->
L'account di archiviazione ora includerà un contenitore BLOB denominato *b2c* nella spiegazione, contenente i modelli wingtiptoys seguenti dello *starter pack*.

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

La tabella seguente descrive lo scopo delle pagine HTML5 precedenti.

| Modello HTML5 | DESCRIZIONE |
|----------------|-------------|
| *phonefactor.html* | Questa pagina può essere usata come modello per una pagina di autenticazione a più fattori. |
| *resetpassword.html* | Questa pagina può essere usata come modello per una pagina Password dimenticata. |
| *selfasserted.html* | Questa pagina può essere usata come modello per una pagina di iscrizione all'account di social networking, una pagina di iscrizione dell'account locale o una pagina di accesso dell'account locale. |
| *unified.html* | Questa pagina può essere usata come modello per una pagina unificata per l'iscrizione o l'accesso. |
| *updateprofile.html* | Questa pagina può essere usata come modello per una pagina di aggiornamento del profilo. |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>Aggiungere un collegamento ai modelli HTML5/CSS al percorso utente

Per aggiungere un collegamento ai modelli HTML5/CSS al percorso utente, è possibile modificare direttamente un criterio personalizzato.

I modelli HTML5/CSS da usare nel percorso utente devono essere specificati in un elenco di definizioni del contenuto che possono essere usate in tali percorsi utente. A tale scopo, un elemento XML *<ContentDefinitions>* facoltativo deve essere dichiarato nella sezione *<BuildingBlocks>* del file XML del criterio personalizzato.

La tabella seguente descrive il set di ID definizione del contenuto riconosciuti dal motore di esperienza di gestione delle identità di Azure AD B2C e il tipo di pagine correlate.

| ID definizione del contenuto | DESCRIZIONE |
|-----------------------|-------------|
| *api.error* | **Pagina di errore**. Questa pagina viene visualizzata quando viene rilevata un'eccezione o un errore. |
| *api.idpselections* | **Pagina di selezione del provider di identità**. Questa pagina contiene un elenco dei provider di identità che l'utente può scegliere durante la procedura di accesso. Sono presenti provider di identità aziendali, provider di identità basati su social network, ad esempio Facebook e Google+, o account locali (basati su indirizzo di posta elettronica o nome utente). |
| *api.idpselections.signup* | **Selezione del provider di identità per l'iscrizione**. Questa pagina contiene un elenco dei provider di identità che l'utente può scegliere durante la procedura di iscrizione. Sono presenti provider di identità aziendali, provider di identità basati su social network, ad esempio Facebook e Google+, o account locali (basati su indirizzo di posta elettronica o nome utente). |
| *api.localaccountpasswordreset* | **Pagina Password dimenticata**. Questa pagina contiene un modulo che l'utente deve compilare per avviare la reimpostazione della password.  |
| *api.localaccountsignin* | **Pagina di accesso dell'account locale**. Questa pagina contiene un modulo di accesso che l'utente deve compilare per eseguire l'accesso con un account locale basato su indirizzo di posta elettronica o nome utente. Il modulo può contenere una casella di input di testo e una casella di immissione della password. |
| *api.localaccountsignup* | **Pagina di iscrizione dell'account locale**. Questa pagina contiene un modulo di iscrizione che l'utente deve compilare per eseguire l'iscrizione a un account locale basato su indirizzo di posta elettronica o nome utente. Il modulo può contenere diversi controlli di input, ad esempio caselle per l'immissione di testo, caselle per l'immissione della password, pulsanti di opzione, caselle a discesa a selezione singola e caselle di controllo con selezione multipla. |
| *api.phonefactor* | **Pagina dell'autenticazione a più fattori**. In questa pagina gli utenti possono verificare il proprio numero di telefono (tramite SMS o chiamata vocale) durante la procedura di iscrizione o di accesso. |
| *api.selfasserted* | **Pagina di iscrizione dell'account locale**. Questa pagina contiene un modulo di iscrizione che l'utente deve compilare per effettuare l'iscrizione usando un account esistente di un provider di identità basato su social network, ad esempio Facebook o Google+. Questa pagina è simile alla precedente pagina di iscrizione dell'account locale, a eccezione dei campi di immissione della password. |
| *api.selfasserted.profileupdate* | **Pagina di aggiornamento del profilo**. Questa pagina contiene un modulo che l'utente può usare per aggiornare il profilo. Questa pagina è simile alla precedente pagina di iscrizione dell'account locale, a eccezione dei campi di immissione della password. |
| *api.signuporsignin* | **Pagina unificata per l'iscrizione o l'accesso**.  Questa pagina consente di gestire sia l'iscrizione che l'accesso degli utenti, che possono usare provider di identità aziendali, provider di identità basati su social network come Facebook o Google+ o account locali.

## <a name="next-steps"></a>Passaggi successivi
[Informazioni di riferimento: conoscere il funzionamento dei criteri personalizzati con il framework di esperienza di gestione delle identità in B2C](active-directory-b2c-reference-custom-policies-understanding-contents.md)
