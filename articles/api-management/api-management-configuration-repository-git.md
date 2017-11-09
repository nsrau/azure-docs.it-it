---
title: Configurare il servizio Gestione API tramite Git - Azure | Documentazione Microsoft
description: Informazioni su come salvare e configurare la configurazione del servizio Gestione API tramite Git
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: mattfarm
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 87d4e3fc4f30d5c7b147fb460fb43367aef19118
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Come salvare e configurare la configurazione del servizio Gestione API tramite Git
> 
> 

Ogni istanza del servizio Gestione API gestisce un database di configurazione che contiene informazioni sulla configurazione e i metadati dell'istanza del servizio. Per apportare modifiche all'istanza del servizio è possibile modificare un'impostazione nel portale di pubblicazione, usare un cmdlet di PowerShell o eseguire una chiamata all'API REST. Oltre a questi metodi, è possibile gestire la configurazione dell'istanza del servizio tramite Git. Ciò consente scenari di gestione del servizio diversi, ad esempio:

* Controllo delle versioni della configurazione: downolad e archiviazione di versioni diverse della configurazione del servizio
* Modifiche in blocco alla configurazione: esecuzione di modifiche in più punti della configurazione del servizio all'interno del repository locale e integrazione delle modifiche nel server con un'unica operazione
* Serie di strumenti e flussi di lavoro Git familiari: possibilità di usare gli strumenti e i flussi di lavoro Git con cui si ha familiarità

Il diagramma seguente offre una panoramica dei diversi modi in cui è possibile configurare un'istanza del servizio Gestione API.

![Configurare Git][api-management-git-configure]

Quando si apportano modifiche al servizio tramite il portale di pubblicazione, i cmdlet di PowerShell o l'API REST, la gestione del database di configurazione del servizio avviene tramite l'endpoint `https://{name}.management.azure-api.net` , come mostrato nella parte destra del diagramma. Il lato sinistro del diagramma illustra come gestire la configurazione del servizio tramite Git e il repository Git per il servizio, disponibile all'indirizzo `https://{name}.scm.azure-api.net`.

I passaggi seguenti offrono una panoramica della gestione dell'istanza del servizio Gestione API tramite Git.

1. Accedere alla configurazione Git nel servizio
2. Salvare il database di configurazione del servizio nel repository Git
3. Clonare il repository Git nel computer locale
4. Estrarre il repository più recente nel computer locale ed eseguire il commit e il push delle modifiche nel repository
5. Distribuire le modifiche dal repository nel database di configurazione del servizio

Questo articolo descrive come abilitare e usare Git per gestire la configurazione del servizio e costituisce un riferimento per i file e le cartelle nel repository Git.

## <a name="access-git-configuration-in-your-service"></a>Accedere alla configurazione Git nel servizio
È possibile visualizzare rapidamente lo stato della configurazione di Git visualizzando l'icona Git nell'angolo superiore destro del portale di pubblicazione. In questo esempio, il messaggio di stato indica che ci sono modifiche non salvate nel repository. Questo avviene perché il database di configurazione del servizio Gestione API non è ancora stato salvato nel repository.

![Stato Git][api-management-git-icon-enable]

Per visualizzare e configurare le impostazioni di configurazione di Git, è possibile fare clic sull'icona Git oppure fare clic sul menu **Security** (Sicurezza) e passare alla scheda **Configuration repository** (Repository configurazioni).

![Abilitare GIT][api-management-enable-git]

> [!IMPORTANT]
> Eventuali segreti non definiti come proprietà verranno archiviati nel repository e rimarranno nella cronologia di questo finché non si disabilita e riabilita l'accesso a Git. Le proprietà rappresentano un luogo sicuro per gestire i valori stringa costanti, segreti inclusi, attraverso tutte le configurazioni e tutti i criteri per le API. Non è quindi necessario archiviarli direttamente nelle istruzioni dei criteri. Per altre informazioni, vedere [Come usare le proprietà nei criteri di Gestione API di Azure](api-management-howto-properties.md).
> 
> 

Per informazioni sull'abilitazione o la disabilitazione dell'accesso a Git mediante l'API REST, vedere la [pagina relativa a questo argomento](https://msdn.microsoft.com/library/dn781420.aspx#EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Per salvare la configurazione del servizio nel repository Git
Il primo passaggio prima della clonazione del repository corrisponde al salvataggio dello stato corrente della configurazione del servizio nel repository. Fare clic su **Save configuration to repository**.

![Salvare la configurazione][api-management-save-configuration]

Apportare le modifiche desiderate nella schermata di conferma e fare clic su **Ok** per salvare.

![Salvare la configurazione][api-management-save-configuration-confirm]

Dopo qualche secondo la configurazione viene salvata e viene visualizzato lo stato della configurazione del repository, con la data e l'ora dell'ultima modifica alla configurazione e l'ultima sincronizzazione tra la configurazione del servizio e il repository.

![Stato della configurazione][api-management-configuration-status]

Dopo che la configurazione è stata salvata nel repository, può essere clonata.

Per informazioni sull'esecuzione di questa operazione tramite l'API REST, vedere la [pagina relativa all'esecuzione del commit di uno snapshot della configurazione tramite l'API REST](https://msdn.microsoft.com/library/dn781420.aspx#CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Per clonare il repository nel computer locale
Per clonare un repository, sono necessari l'URL del repository, un nome utente e una password. Il nome utente e l'URL sono visualizzati nella parte superiore della scheda **Configuration repository** .

![Clonare in Git][api-management-configuration-git-clone]

La password viene generata nella parte inferiore della scheda **Configuration repository** .

![Generare password][api-management-generate-password]

Per generare una password, verificare prima di tutto che il campo **Expiry** (Scadenza) sia impostato sulla data e sull'ora di scadenza desiderate e quindi fare clic su **Generate Token** (Genera token).

![Password][api-management-password]

> [!IMPORTANT]
> Prendere nota della password. Una volta chiusa questa pagina, la password non verrà più visualizzata.
> 
> 

Gli esempi seguenti usano lo strumento Git Bash di [Git per Windows](http://www.git-scm.com/downloads) ma è possibile usare qualsiasi strumento Git con cui si abbia familiarità.

Aprire lo strumento Git nella cartella desiderata ed eseguire il comando seguente per clonare il repository Git nel computer locale, usando il comando fornito dal portale di pubblicazione.

```
git clone https://bugbashdev4.scm.azure-api.net/
```

Quando richiesto,specificare il nome utente e la password.

Se si ricevono errori, provare a modificare il comando `git clone` includendo il nome utente e password, come illustrato nell'esempio seguente.

```
git clone https://username:password@bugbashdev4.scm.azure-api.net/
```

Se viene generato un errore, provare a codificare con URL la parte della password del comando. Un metodo rapido per eseguire questa operazione consiste nell'aprire Visual Studio, eseguendo il comando seguente nella **finestra di controllo immediato**. Per aprire la **finestra di controllo immediato**, aprire qualsiasi soluzione o progetto in Visual Studio oppure creare una nuova applicazione console vuota e quindi **Finestre** e quindi **Controllo immediato** dal menu **Debug**.

```
?System.NetWebUtility.UrlEncode("password from publisher portal")
```

Per creare il comando git, usare la password codificata con il nome utente e il percorso del repository.

```
git clone https://username:url encoded password@bugbashdev4.scm.azure-api.net/
```

Una volta clonato il repository, è possibile visualizzarlo e usarlo nel file system locale. Per altre informazioni, vedere [Informazioni di riferimento sulla struttura di file e cartelle del repository Git locale](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Per aggiornare il repository locale con la configurazione dell'istanza del servizio più recente
Se si apportano modifiche all'istanza del servizio Gestione API nel portale di pubblicazione o tramite l'API REST, è necessario salvare le modifiche nel repository prima di aggiornare il repository locale con le modifiche più recenti. A tale scopo, fare clic su **Save configuration to repository** (Salva configurazione in repository) nella scheda **Configuration repository** (Repository configurazioni) del portale di pubblicazione e quindi eseguire questo comando nel repository locale.

```
git pull
```

Prima di eseguire `git pull` assicurarsi di trovarsi nella cartella del repository locale. Se è appena stato eseguito il comando `git clone` , è necessario modificare la directory con quella del repository tramite un comando simile al seguente.

```
cd bugbashdev4.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Per eseguire il push delle modifiche dal repository locale al repository del server
Per eseguire il push delle modifiche dal repository locale al repository del server, è prima necessario eseguire il commit delle modifiche stesse. Per eseguire il commit delle modifiche, aprire lo strumento dei comandi Git, passare alla directory del repository locale ed eseguire i comandi seguenti.

```
git add --all
git commit -m "Description of your changes"
```

Per eseguire il push di tutti i commit nel server, eseguire il comando seguente.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Per distribuire le modifiche alla configurazione del servizio all'istanza del servizio Gestione API
Dopo il commit e il push delle modifiche locali nel repository del server, è possibile distribuire le modifiche all'istanza del servizio Gestione API.

![Distribuisci][api-management-configuration-deploy]

Per informazioni sull'esecuzione di questa operazione tramite l'API REST, vedere la [pagina relativa alla distribuzione delle modifiche al database di configurazione tramite l'API REST](https://docs.microsoft.com/en-us/rest/api/apimanagement/tenantconfiguration).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Informazioni di riferimento sulla struttura di file e cartelle del repository Git locale
I file e cartelle nel repository Git locale contengono le informazioni di configurazione dell'istanza del servizio.

| Item | Descrizione |
| --- | --- |
| Cartella api-management radice |Contiene la configurazione di livello superiore per l'istanza del servizio |
| Cartella apis |Contiene la configurazione per le API nell'istanza del servizio |
| Cartella groups |Contiene la configurazione per i gruppi nell'istanza del servizio |
| Cartella policies |Contiene i criteri dell'istanza del servizio |
| Cartella portalStyles |Contiene la configurazione delle personalizzazioni del portale per sviluppatori nell'istanza del servizio |
| Cartella products |Contiene la configurazione per i prodotti nell'istanza del servizio |
| Cartella templates |Contiene la configurazione per i modelli nell'istanza del servizio |

Ogni cartella può contenere uno o più file e in alcuni casi una o più cartelle, ad esempio una cartella per ogni API, prodotto o gruppo. I file all'interno di ogni cartella sono specifici per il tipo di entità descritto dal nome della cartella.

| Tipo file | Scopo |
| --- | --- |
| json |Informazioni di configurazione dell'entità corrispondente |
| html |Descrizioni delle entità, spesso visualizzate nel portale per sviluppatori |
| xml |Policy statements |
| css |Fogli di stile per la personalizzazione del portale per sviluppatori |

Questi file possono essere creati, eliminati, modificati e gestiti nel file system locale e le modifiche possono essere ridistribuite nell'istanza del servizio Gestione API.

> [!NOTE]
> Le entità seguenti non sono contenute nel repository Git e non possono essere configurate tramite Git.
> 
> * Utenti
> * Sottoscrizioni
> * Proprietà
> * Entità del portale per sviluppatori diverse dagli stili
> 
> 

### <a name="root-api-management-folder"></a>Cartella api-management radice
La cartella `api-management` radice contiene un file `configuration.json` che a propria volta contiene informazioni di livello superiore relative all'istanza del servizio nel formato seguente.

```json
{
  "settings": {
    "RegistrationEnabled": "True",
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": "False",
    "UserRegistrationTermsConsentRequired": "False",
    "DelegationEnabled": "False",
    "DelegationUrl": "",
    "DelegatedSubscriptionEnabled": "False",
    "DelegationValidationKey": ""
  },
  "$ref-policy": "api-management/policies/global.xml"
}
```

Le prime quattro impostazioni (`RegistrationEnabled`, `UserRegistrationTerms`, `UserRegistrationTermsEnabled` e `UserRegistrationTermsConsentRequired`) sono mappate alle impostazioni seguenti della scheda **Identities** (Identità) della sezione **Security** (Sicurezza).

| Impostazione | Mapping a |
| --- | --- |
| RegistrationEnabled |**Redirect anonymous users to**  |
| UserRegistrationTerms |**Terms of use on user signup**  |
| UserRegistrationTermsEnabled |**Show terms of use on signup page** |
| UserRegistrationTermsConsentRequired |**Richiedi consenso**  |

![Impostazioni di identità][api-management-identity-settings]

Le quattro impostazioni successive (`DelegationEnabled`, `DelegationUrl`, `DelegatedSubscriptionEnabled` e `DelegationValidationKey`) sono mappate alle impostazioni seguenti della scheda **Delegation** (Delega) della sezione **Security** (Sicurezza).

| Impostazione | Mapping a |
| --- | --- |
| DelegationEnabled |Casella di controllo **Delegate sign-in & sign-up** (Delega accesso e iscrizione) |
| DelegationUrl |**Delegation endpoint URL** |
| DelegatedSubscriptionEnabled |**Delegate product subscription** |
| DelegationValidationKey |**Delegate Validation Key** |

![Impostazioni di delega][api-management-delegation-settings]

L'impostazione finale, `$ref-policy`, esegue il mapping al file di istruzioni dei criteri globali per l'istanza del servizio.

### <a name="apis-folder"></a>Cartella apis
La cartella `apis` contiene, per ogni API nell'istanza del servizio, una cartella contenente a sua volta gli elementi seguenti.

* `apis\<api name>\configuration.json`: configurazione dell'API. Contiene informazioni relative all'URL del servizio back-end e alle operazioni. Si tratta delle stesse informazioni che verrebbero restituite se fosse necessario [ottenere un'API specifica](https://msdn.microsoft.com/library/azure/dn781423.aspx#GetAPI) con `export=true` nel formato `application/json`.
* `apis\<api name>\api.description.html`: descrizione dell'API. Corrisponde alla proprietà `description` dell'[entità relativa all'API](https://msdn.microsoft.com/library/azure/dn781423.aspx#EntityProperties).
* `apis\<api name>\operations\`: questa cartella contiene i file `<operation name>.description.html` mappati alle operazioni nell'API. Ogni file contiene la descrizione di una singola operazione dell'API che esegue il mapping alla proprietà `description` dell' [entità relativa all'operazione](https://msdn.microsoft.com/library/azure/dn781423.aspx#OperationProperties) nell'API REST.

### <a name="groups-folder"></a>Cartella groups
La cartella `groups` contiene una cartella per ogni gruppo definito nell'istanza del servizio.

* `groups\<group name>\configuration.json`: configurazione del gruppo. Si tratta delle stesse informazioni che verrebbero restituite se fosse necessario chiamare l'operazione per [ottenere un gruppo specifico](https://msdn.microsoft.com/library/azure/dn776329.aspx#GetGroup) .
* `groups\<group name>\description.html`: descrizione del gruppo. Corrisponde alla proprietà `description` dell'[entità relativa al gruppo](https://msdn.microsoft.com/library/azure/dn776329.aspx#EntityProperties).

### <a name="policies-folder"></a>Cartella policies
La cartella `policies` contiene le istruzioni dei criteri per l'istanza del servizio.

* `policies\global.xml` : contiene i criteri definiti in ambito globale per l'istanza del servizio.
* `policies\apis\<api name>\`: se sono presenti criteri definiti nell'ambito delle API, tali criteri sono contenuti in questa cartella.
* Cartella `policies\apis\<api name>\<operation name>\`: se sono presenti criteri definiti nell'ambito delle operazioni, tali criteri sono contenuti in questa cartella all'interno dei file `<operation name>.xml` mappati alle istruzioni dei criteri per ogni operazione.
* `policies\products\`: se sono presenti criteri definiti nell'ambito dei prodotti, tali criteri sono contenuti in questa cartella, contenente file `<product name>.xml` mappati alle istruzioni dei criteri per ogni prodotto.

### <a name="portalstyles-folder"></a>Cartella portalStyles
La cartella `portalStyles` contiene la configurazione e i fogli di stile delle personalizzazioni del portale per sviluppatori per l'istanza del servizio.

* `portalStyles\configuration.json` : contiene i nomi dei fogli di stile usati dal portale per sviluppatori
* `portalStyles\<style name>.css`: ogni file `<style name>.css` contiene stili per il portale per sviluppatori (per impostazione predefinita, `Preview.css` e `Production.css`).

### <a name="products-folder"></a>Cartella products
La cartella `products` contiene una cartella per ogni prodotto definito nell'istanza del servizio.

* `products\<product name>\configuration.json`: configurazione del prodotto. Si tratta delle stesse informazioni che verrebbero restituite se fosse necessario chiamare l'operazione per [ottenere un prodotto specifico](https://msdn.microsoft.com/library/azure/dn776336.aspx#GetProduct) .
* `products\<product name>\product.description.html`: descrizione del prodotto. Corrisponde alla proprietà `description` dell'[entità relativa al prodotto](https://msdn.microsoft.com/library/azure/dn776336.aspx#Product) nell'API REST.

### <a name="templates"></a>Modelli
La cartella `templates` contiene la configurazione per i [modelli di posta elettronica](api-management-howto-configure-notifications.md) dell'istanza del servizio.

* `<template name>\configuration.json` : configurazione del modello di posta elettronica.
* `<template name>\body.html` : corpo del modello di posta elettronica.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su altri metodi di gestione dell'istanza del servizio, vedere:

* Gestire l'istanza del servizio con i cmdlet di PowerShell seguenti
  * [Informazioni di riferimento sui cmdlet di PowerShell per la distribuzione dei servizi](https://msdn.microsoft.com/library/azure/mt619282.aspx)
  * [Informazioni di riferimento sui cmdlet di PowerShell per la gestione dei servizi](https://msdn.microsoft.com/library/azure/mt613507.aspx)
* Gestire l'istanza del servizio nel portale di pubblicazione
  * [Gestire la prima API](api-management-get-started.md)
* Gestire l'istanza del servizio tramite l'API REST
  * [Informazioni di riferimento sull'API REST di Gestione API](https://msdn.microsoft.com/library/azure/dn776326.aspx)

## <a name="watch-a-video-overview"></a>Guardare un video introduttivo
> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Configuration-over-Git/player]
> 
> 

[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png




