---
title: File di inclusione
description: File di inclusione
documentationcenter: partner-center-commercial-marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 08/13/2019
ms.author: dsindona
ms.custom: include file
ms.openlocfilehash: f2446a924ceed37c51779efc9d9e94c0252a2067
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80321833"
---
La scheda **Test drive** consente di impostare una dimostrazione (o "test drive") che consentirà ai clienti di provare l'offerta prima di impegnarsi ad acquistarla. Ulteriori informazioni sono riportate nell'articolo [Che cos'è Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Se non si desidera più fornire un test drive per l'offerta, tornare alla pagina **Impostazione offerta** e deselezionare **Abilita test drive**.

### <a name="technical-configuration"></a>Configurazione tecnica
Sono disponibili i seguenti tipi di test drive, ognuno con i propri requisiti di configurazione tecnica.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [App per la logica](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (configurazione tecnica non necessaria)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Configurazione tecnica per il test drive di Azure Resource ManagerTechnical configuration for Azure Resource Manager test drive

Modello di distribuzione che contiene tutte le risorse di Azure che costituiscono la soluzione. I prodotti che si adattano a questo scenario usano solo le risorse di Azure.Products that fit this scenario use only Azure resources. Altre informazioni sulla configurazione di un test drive di [Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Aree** (obbligatorio): attualmente sono presenti 26 aree supportate da Azure in cui è possibile redimare il test drive. In genere, è consigliabile rendere disponibile l'unità di test nelle aree in cui si prevede il maggior numero di clienti, in modo che possano selezionare l'area più vicina per ottenere prestazioni ottimali. È necessario assicurarsi che la sottoscrizione sia autorizzata a distribuire tutte le risorse necessarie in ognuna delle aree selezionate.

- **Istanze**: Selezionare il tipo (caldo o freddo) e il numero di istanze disponibili, che verranno moltiplicate per il numero di regioni in cui l'offerta è disponibile.

**Hot**: Questo tipo di istanza viene distribuito e in attesa di accesso per ogni area selezionata. I clienti possono accedere istantaneamente alle istanze *Hot* di un test drive, anziché dover attendere una distribuzione. Il compromesso è che queste istanze sono sempre in esecuzione nella sottoscrizione di Azure, comportando quindi un costo relativo al tempo di attività più elevato. È consigliabile avere almeno un'istanza *Hot,* poiché la maggior parte dei clienti non desidera attendere le distribuzioni complete, con conseguente calo dell'utilizzo dei clienti se non è disponibile alcuna istanza *Hot.*

**Cold**: Questo tipo di istanza rappresenta il numero totale di istanze che possono essere distribuite per ogni area. Le istanze fredde richiedono la distribuzione dell'intero modello di Test Drive Resource Manager quando un cliente richiede l'unità di test, pertanto le istanze *Cold* sono molto più lente da caricare rispetto alle istanze *Hot.* Il compromesso è che è necessario pagare solo per la durata del test drive, non è sempre in esecuzione nella sottoscrizione di Azure come con un'istanza *Hot.The* tradeoff is that You only have to pay for the duration of the test drive, it is *not* always running on your Azure subscription as with a Hot instance.

- **Test test test modello di Azure Resource Manager:** caricare il file .zip contenente il modello di Azure Resource Manager.Test drive Azure Resource Manager template : Upload the .zip containing your Azure Resource Manager template.  Per altre informazioni sulla creazione di un modello di Azure Resource Manager, vedere l'articolo delle guide rapide Creare e distribuire modelli di [Azure Resource Manager tramite il portale](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)di Azure.

- **Durata dell'unità** di test (obbligatorio): immettere il periodo di tempo in cui il Test Drive rimarrà attivo, in numero di ore. Il test drive termina automaticamente alla fine di questo periodo di tempo. Questa durata può essere impostata solo da un numero intero di ore (ad esempio "2" ore, "1,5" non è valido).

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Configurazione tecnica per il test drive di Dynamics 365

Microsoft può rimuovere la complessità della configurazione di un test drive ospitando e mantenendo il provisioning e la distribuzione del servizio utilizzando questo tipo di test drive. La configurazione per questo tipo di test drive ospitato è la stessa indipendentemente dal fatto che il test drive sia destinato a un gruppo di destinatari Business Central, Customer Engagement o Operations.

- Numero massimo di **test simultanei** (obbligatorio): impostare il numero massimo di clienti che possono utilizzare il test drive contemporaneamente. Ogni utente simultaneo utilizzerà una licenza di Dynamics 365 mentre il test drive è attivo, pertanto dovrai assicurarti di disporre di un numero sufficiente di licenze disponibili per supportare il set di limiti massimo. Valore consigliato da 3 a 5.

- **Durata dell'unità** di test (obbligatorio): immettere il periodo di tempo in cui il Test Drive rimarrà attivo definendo il numero di ore. Dopo queste ore, la sessione terminerà e non utilizzerà più una delle licenze. Consigliamo un valore di 2-24 ore a seconda della complessità della tua offerta. Questa durata può essere impostata solo da un numero intero di ore (ad esempio "2" ore, "1,5" non è valido).  L'utente può richiedere una nuova sessione se ha esaurito il tempo e desidera accedere nuovamente al test drive.

- **URL istanza** (obbligatorio): l'URL in cui il cliente inizierà il test drive. In genere l'URL dell'istanza di Dynamics 365 che `https://testdrive.crm.dynamics.com`esegue l'app con i dati di esempio installati (ad esempio).

- **URL dell'API Web dell'istanza** (obbligatorio): recupera l'URL dell'API Web per l'istanza di Dynamics 365 accedendo al tuo account Microsoft 365 e accedendo a **Impostazioni** \&gt; **Personalizzazione** \&gt; **Risorse** \&per sviluppatori gt; **API Web dell'istanza (URL radice del servizio)**, `https://testdrive.crm.dynamics.com/api/data/v9.0`copiare l'URL trovato qui (ad esempio ).

- **Nome ruolo** (obbligatorio): specificare il nome del ruolo di sicurezza definito nel test drive Dynamics 365 personalizzato. Questo verrà assegnato all'utente durante il test drive (ad esempio test-drive-role).

#### <a name="technical-configuration-for-logic-app-test-drive"></a>Configurazione tecnica per l'unità di test dell'app per la logicaTechnical configuration for Logic app test drive

Tutti i prodotti personalizzati devono utilizzare questo tipo di modello di distribuzione di test drive che include una varietà di architetture di soluzioni complesse. Per altre informazioni sull'impostazione dei test di App logica, vedere Operations and Customer Engagement su GitHub.For more information about setting up Logic App test drives, visit [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) and [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) on GitHub.

- **Area** (obbligatorio, elenco a discesa a selezione singola): attualmente sono disponibili 26 aree supportate da Azure in cui è possibile redimare il test drive. Le risorse per l'app per la logica verranno distribuite nell'area selezionata. Se l'app per la logica include risorse personalizzate archiviate in un'area specifica, assicurarsi che tale area sia selezionata qui. Il modo migliore per eseguire questa operazione consiste nel distribuire completamente l'app per la logica in locale nella sottoscrizione di Azure nel portale e verificare che funzioni correttamente prima di effettuare questa selezione.

- Numero massimo di **test simultanei** (obbligatorio): impostare il numero massimo di clienti che possono utilizzare il test drive contemporaneamente. Questi test drive sono già distribuiti, consentendo ai clienti di accedervi immediatamente senza attendere una distribuzione.

- **Durata dell'unità** di test (obbligatorio): immettere il periodo di tempo in cui il Test Drive rimarrà attivo, in numero di ore. L'unità di prova termina automaticamente al termine di questo periodo di tempo.

- **Nome del gruppo** di risorse di Azure (obbligatorio): immettere il nome del gruppo di risorse di [Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) in cui viene salvata l'unità di test dell'app per la logica.

- **Nome app per la logica** di Azure (obbligatorio): immettere il nome dell'app per la logica che assegna l'unità di test all'utente. Questa app per la logica deve essere salvata nel gruppo di risorse di Azure precedente.

- **Deprovisioning del nome dell'app per** la logica (obbligatorio): immettere il nome dell'app per la logica che esegue il deprovisioning del test drive al termine del cliente. Questa app per la logica deve essere salvata nel gruppo di risorse di Azure precedente.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configurazione tecnica non necessaria per i test drive di Power BITechnical configuration not required for Power BI test drives

I prodotti che vogliono dimostrare un oggetto visivo interattivo di Power BI possono usare un collegamento incorporato per condividere un dashboard personalizzato come test drive, senza richiedere ulteriori configurazioni tecniche. Altre informazioni sulla configurazione delle app modello di[Power BI.](https://docs.microsoft.com/power-bi/service-template-apps-overview)

### <a name="deployment-subscription-details"></a>Dettagli sottoscrizione distribuzione

Per distribuire test Drive per conto dell'utente, creare e fornire una sottoscrizione di Azure separata e univoca. (Non richiesto per i test drive di Power BI).

- **ID sottoscrizione di Azure** (obbligatorio per Azure Resource Manager e app per la logica): immettere l'ID sottoscrizione per concedere l'accesso ai servizi dell'account di Azure per la creazione di report e la fatturazione dell'utilizzo delle risorse. È consigliabile [creare una sottoscrizione](https://docs.microsoft.com/azure/billing/billing-create-subscription) di Azure separata da usare per i test drive se non ne è già presente una. È possibile trovare l'ID sottoscrizione di Azure accedendo al portale di [Azure](https://portal.azure.com/) e passando alla scheda **Sottoscrizioni** del menu a sinistra. Selezionando la scheda verrà visualizzato l'ID sottoscrizione (ad esempio "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID tenant di Azure AD** (obbligatorio): immettere l'ID [tenant](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)di Azure Active Directory (AD). Per trovare questo ID, accedere al portale di [Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare **Proprietà** , quindi cercare il numero **ID directory** elencato (ad esempio 50c464d3-4930-494c-963c-1e951d15360e). È inoltre possibile cercare l'ID tenant dell'organizzazione [https://www.whatismytenantid.com](https://www.whatismytenantid.com)utilizzando l'URL del nome di dominio all'indirizzo: .

- **Nome tenant di Azure AD** (obbligatorio per Dynamic 365): immettere il nome di Azure Active Directory (AD). Per trovare questo nome, accedere al portale di [Azure](https://portal.azure.com/), nell'angolo superiore destro il nome del tenant verrà elencato sotto il nome dell'account.

- **ID app Azure AD** (obbligatorio): immettere [l'ID applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)di Azure Active Directory (AD). Per trovare questo ID, accedere al portale di [Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare **Registrazioni app**, quindi cercare il numero ID **applicazione** elencato (ad esempio 50c464d3-4930-494c-963c-1e951d15360e).

- **Segreto client dell'app Azure AD** (obbligatorio): immettere il [segreto client](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)dell'applicazione Azure AD.Azure AD app client secret (required): Enter your Azure AD application client secret . Per trovare questo valore, accedere al portale di [Azure](https://portal.azure.com/). Seleziona la scheda **Azure Active Directory** nel menu a sinistra, seleziona **Registrazioni app**, quindi seleziona l'app di test drive. Selezionare quindi **Certificati e segreti**, Nuovo **segreto client**, immettere una descrizione, **selezionare Mai** in **Scadenza**, quindi scegliere **Aggiungi**. Assicurarsi di copiare il valore. (Non uscire dalla pagina prima di eseguire questa operazione, altrimenti non avrai accesso al valore).

Ricordati di **salvare** prima di passare alla sezione successiva!

### <a name="test-drive-listings-optional"></a>Elenchi di test drive (facoltativo)

L'opzione **Elenchi Test Drive** disponibile nella scheda Test **drive** visualizza le lingue (e i mercati) in cui è disponibile il test drive, attualmente l'inglese (Stati Uniti) è l'unica posizione disponibile. Inoltre, in questa pagina viene visualizzato lo stato dell'elenco specifico della lingua e la data/ora in cui è stato aggiunto. Dovrai definire i dettagli del test drive (descrizione, manuale utente, video, ecc.) per ogni lingua/mercato.

- **Descrizione** (obbligatorio): descrivere il test drive, ciò che verrà dimostrato, gli obiettivi per l'utente da sperimentare, le funzionalità da esplorare e tutte le informazioni pertinenti che consentono all'utente di determinare se acquistare l'offerta. In questo campo è possibile immettere fino a 3.000 caratteri di testo. 

- **Accedere alle informazioni** (necessarie per Azure Resource Manager e test drive della logica): spiegare ciò che un cliente deve sapere per poter accedere e usare questo test drive. Esaminare uno scenario per l'utilizzo dell'offerta e esattamente ciò che il cliente deve sapere per accedere alle funzionalità durante il test drive. In questo campo è possibile immettere fino a 10.000 caratteri di testo.

- **Manuale utente** (obbligatorio): una procedura dettagliata dell'esperienza di test drive. Il Manuale dell'utente dovrebbe coprire esattamente ciò che si desidera che il cliente di ottenere da sperimentare il test drive e servire come riferimento per tutte le domande che possono avere. Il file deve essere in formato PDF e deve essere denominato (255 caratteri max) dopo il caricamento.

- **Video: aggiungi video** (facoltativo): i video possono essere caricati su YouTube o Vimeo e referenziati qui con un link e un'immagine di anteprima (533 x 324 pixel) in modo che un cliente possa visualizzare una passeggiata di informazioni per aiutarli a comprendere meglio il test drive, incluso come utilizzare con successo le funzionalità della tua offerta e comprendere gli scenari che ne evidenziano i vantaggi.
  - **Nome** (obbligatorio)
  - **URL (solo YouTube o Vimeo)** (obbligatorio)
  - **Miniatura (533 x 324px):** il file di immagine deve essere in formato PNG.

Selezionare **Salva** dopo aver completato questi campi.
