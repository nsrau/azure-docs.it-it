---
title: file di inclusione
description: file di inclusione
services: commercial marketplace
documentationcenter: partner-center-commercial-marketplace
author: qianw211
manager: evansma
editor: ''
ms.service: marketplace
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/13/2019
ms.author: v-qiwe
ms.custom: include file
ms.openlocfilehash: ee4d253eaa50b4299f4b912ccacbd50307d6fdc5
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72601006"
---
La scheda **test drive** consente di configurare una dimostrazione (o "test drive") che consentirà ai clienti di provare l'offerta prima di eseguire il commit per acquistarla. Per altre informazioni, vedere l'articolo relativo a [test drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Se non si desidera più fornire un test drive per l'offerta, tornare alla pagina di **installazione dell'offerta** e deselezionare **Abilita Test Drive**.

### <a name="technical-configuration"></a>Configurazione tecnica
Sono disponibili i tipi di unità di test seguenti, ognuno con requisiti di configurazione tecnici specifici.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [App per la logica](#technical-configuration-for-logic-app-test-drive)
- [Power bi](#technical-configuration-not-required-for-power-bi-test-drives) (configurazione tecnica non necessaria)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Configurazione tecnica per Azure Resource Manager test drive

Un modello di distribuzione che contiene tutte le risorse di Azure che costituiscono la soluzione. I prodotti che soddisfano questo scenario usano solo le risorse di Azure. Altre informazioni sulla configurazione di un [Azure Resource Manager Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regions** (obbligatorio): attualmente sono presenti 26 aree supportate da Azure in cui è possibile rendere disponibili le test drive. In genere, è consigliabile rendere le test drive disponibili nelle aree in cui si prevede il maggior numero di clienti, in modo che possano selezionare l'area più vicina per ottenere prestazioni ottimali. È necessario assicurarsi che alla sottoscrizione sia consentita la distribuzione di tutte le risorse necessarie in ognuna delle aree selezionate.

- **Istanze**: selezionare il tipo (Hot o Cold) e il numero di istanze disponibili, che verranno moltiplicate per il numero di aree in cui è disponibile l'offerta.

**Hot**: questo tipo di istanza viene distribuito e in attesa di accesso per area selezionata. I clienti possono accedere immediatamente alle istanze a *caldo* di una test drive, anziché dover attendere una distribuzione. Il compromesso è che queste istanze sono sempre in esecuzione nella sottoscrizione di Azure, comportando quindi un costo relativo al tempo di attività più elevato. Si consiglia di disporre di almeno un'istanza di accesso frequente, in quanto la maggior parte dei clienti non desidera attendere le distribuzioni complete, causando un calo nell'utilizzo del cliente se non è *disponibile un'istanza* a *caldo* .

**Cold**: questo tipo di istanza rappresenta il numero totale di istanze che possono essere distribuite per ogni area. Per le istanze a freddo è necessario che l'intero test drive Gestione risorse modello venga distribuito quando un cliente richiede l'test drive, in modo che le istanze a *freddo* risultino molto più lente del caricamento rispetto alle istanze a *caldo* . Il compromesso è dovuto al fatto che è necessario pagare solo per la durata del test drive, *non* è sempre in esecuzione nella sottoscrizione di Azure come con *un'istanza* attiva.

- **Test drive Azure Resource Manager modello**: caricare il file zip contenente il modello di Azure Resource Manager.  Per altre informazioni sulla creazione di un modello di Azure Resource Manager, vedere l'articolo introduttivo [creare e distribuire modelli di Azure Resource Manager tramite il portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Durata test drive** (obbligatorio): immettere il periodo di tempo in cui il test drive resterà attivo, in ore. Il test drive termina automaticamente alla fine di questo periodo di tempo. Questa durata può essere impostata solo per un numero intero di ore (ad esempio, "2" ore, "1,5" non è valido).

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Configurazione tecnica per Dynamics 365 test drive

Microsoft può rimuovere la complessità della configurazione di un test drive ospitando e gestendo il provisioning e la distribuzione del servizio usando questo tipo di test drive. La configurazione di questo tipo di test drive ospitata è la stessa, indipendentemente dal fatto che la test drive sia destinata a un gruppo di lavoro centrale, Customer Engagement o Operations.

- Numero massimo di **unità di test simultanee** (obbligatorio): impostare il numero massimo di clienti che possono usare la test drive alla volta. Ogni utente simultaneo utilizzerà una licenza Dynamics 365 mentre la test drive è attiva, pertanto sarà necessario assicurarsi che siano disponibili sufficienti licenze per supportare il limite massimo impostato. Valore consigliato da 3 a 5.

- **Durata test drive** (obbligatorio): immettere il periodo di tempo per cui il test drive resterà attivo definendo il numero di ore. Dopo questo numero di ore, la sessione terminerà e non utilizzerà più una delle licenze. È consigliabile un valore di 2-24 ore a seconda della complessità dell'offerta. Questa durata può essere impostata solo per un numero intero di ore (ad esempio, "2" ore, "1,5" non è valido).  L'utente può richiedere una nuova sessione se esaurisce il tempo e desidera accedere nuovamente al test drive.

- **URL istanza** (obbligatorio): URL da cui il cliente inizierà a test drive. In genere l'URL dell'istanza di Dynamics 365 che esegue l'app con i dati di esempio installati, ad esempio  https://testdrive.crm.dynamics.com).

- **URL dell'API Web dell'istanza** (obbligatorio): recuperare l'URL dell'API Web per l'istanza di Dynamics 365 accedendo all'account Microsoft 365 e passando alle **Impostazioni** \&gt; @No__t_4gt di **personalizzazione** ; **Risorse** per gli sviluppatori \&gt; **API Web dell'istanza (URL radice del servizio)** , copiare l'URL trovato qui, ad esempio  https://testdrive.crm.dynamics.com/api/data/v9.0).

- **Nome ruolo** (obbligatorio): specificare il nome del ruolo di sicurezza definito nell'test drive Dynamics 365 personalizzato. Questa operazione verrà assegnata all'utente durante il test drive, ad esempio test-drive-Role.

#### <a name="technical-configuration-for-logic-app-test-drive"></a>Configurazione tecnica per test drive app per la logica

Tutti i prodotti personalizzati devono usare questo tipo di modello di distribuzione test drive che include un'ampia gamma di architetture di soluzioni complesse. Per altre informazioni sulla configurazione di unità di test per l'app per la logica, vedere [operazioni](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) e [coinvolgimento dei clienti](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) su GitHub.

- **Region** (obbligatorio, elenco a discesa a selezione singola): attualmente sono presenti 26 aree supportate da Azure in cui è possibile rendere disponibili le test drive. Le risorse per l'app per la logica verranno distribuite nell'area selezionata. Se l'app per la logica contiene risorse personalizzate archiviate in un'area specifica, assicurarsi che l'area sia selezionata qui. Il modo migliore per eseguire questa operazione consiste nel distribuire completamente l'app per la logica localmente nella sottoscrizione di Azure nel portale e verificare che funzioni correttamente prima di effettuare questa selezione.

- Numero massimo di **unità di test simultanee** (obbligatorio): impostare il numero massimo di clienti che possono usare la test drive alla volta. Queste unità di test sono già distribuite, consentendo ai clienti di accedervi immediatamente senza attendere una distribuzione.

- **Durata test drive** (obbligatorio): immettere il periodo di tempo in cui il test drive resterà attivo, in ore. Il test drive termina automaticamente al termine di questo periodo di tempo.

- **Nome del gruppo di risorse di Azure** (obbligatorio): immettere il nome del [gruppo di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) in cui viene salvata l'App per la logica test drive.

- **Nome dell'app** per la logica di Azure (obbligatorio): immettere il nome dell'app per la logica che assegna la test drive all'utente. Questa app per la logica deve essere salvata nel gruppo di risorse di Azure precedente.

- **Deprovisioning app per la logica nome** (obbligatorio): immettere il nome dell'app per la logica che annulla il provisioning del test drive al termine del cliente. Questa app per la logica deve essere salvata nel gruppo di risorse di Azure precedente.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configurazione tecnica non necessaria per Power BI test drive

I prodotti che vogliono dimostrare un oggetto visivo Power BI interattivo possono usare un collegamento incorporato per condividere un dashboard personalizzato come test drive, non sono necessarie altre configurazioni tecniche. Altre informazioni sulla configurazione di[Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview) app modello.

### <a name="deployment-subscription-details"></a>Dettagli sottoscrizione della distribuzione

Per distribuire il test drive per conto dell'utente, creare e fornire una sottoscrizione di Azure separata e univoca. (Non necessario per Power BI test drive).

- **ID sottoscrizione di Azure** (obbligatorio per Azure Resource Manager e app per la logica): immettere l'ID sottoscrizione per concedere l'accesso ai servizi dell'account di Azure per la fatturazione e la creazione di report sull'utilizzo delle risorse. È consigliabile [creare una sottoscrizione di Azure separata](https://docs.microsoft.com/azure/billing/billing-create-subscription) da usare per le unità di test se non ne è già presente uno. È possibile trovare l'ID sottoscrizione di Azure accedendo al [portale di Azure](https://portal.azure.com/) e passando alla scheda **sottoscrizioni** del menu a sinistra. Selezionando la scheda, viene visualizzato l'ID sottoscrizione, ad esempio "a83645ac-1234-5AB6-6789-1h234g764ghty".

- **Azure ad ID tenant** (obbligatorio): immettere l' [id tenant](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)di Azure Active Directory (ad). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare **Proprietà** , quindi cercare il numero **ID directory** elencato (ad esempio 50c464d3-4930-494c-963C-1e951d15360e). È anche possibile cercare l'ID tenant dell'organizzazione usando l'URL del nome di dominio all'indirizzo: [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **Azure ad nome del tenant** (obbligatorio per la 365 dinamica): immettere il nome del Azure Active Directory (ad). Per trovare questo nome, accedere al [portale di Azure](https://portal.azure.com/), nell'angolo in alto a destra il nome del tenant verrà elencato sotto il nome dell'account.

- **Azure ad ID app** (obbligatorio): immettere l' [id applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)di Azure Active Directory (ad). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare **registrazioni app**, quindi cercare il numero **ID applicazione** elencato (ad esempio 50c464d3-4930-494c-963C-1e951d15360e).

- **Azure ad segreto client App** (obbligatorio): immettere il [segreto client](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)dell'applicazione Azure ad. Per trovare questo valore, accedere al [portale di Azure](https://portal.azure.com/). Selezionare la scheda **Azure Active Directory** nel menu a sinistra, selezionare **registrazioni app**, quindi selezionare l'app test drive. Selezionare quindi **certificati e segreti**, fare clic **su nuovo segreto client**, immettere una descrizione, selezionare **mai** sotto **scadenza**, quindi scegliere **Aggiungi**. Assicurarsi di copiare il valore. Non uscire dalla pagina prima di eseguire questa operazione, altrimenti non sarà possibile accedere al valore.

Ricordarsi di **salvare** prima di procedere alla sezione successiva.

### <a name="test-drive-listings-optional"></a>Elenchi di test drive (facoltativo)

L'opzione per gli **elenchi di test drive** disponibile nella scheda **test drive** Visualizza le lingue (e i mercati) in cui è disponibile la test drive, attualmente in inglese (Stati Uniti) è l'unica posizione disponibile. Inoltre, in questa pagina viene visualizzato lo stato dell'elenco specifico della lingua e della data/ora in cui è stato aggiunto. È necessario definire i dettagli del test drive (descrizione, manuale dell'utente, video e così via) per ogni lingua/mercato.

- **Descrizione** (obbligatoria): descrivere la test drive, gli elementi che verranno illustrati, gli obiettivi dell'utente per sperimentare, le funzionalità da esplorare e tutte le informazioni rilevanti per aiutare l'utente a determinare se acquisire l'offerta. In questo campo è possibile immettere fino a 3.000 caratteri di testo. 

- **Informazioni di accesso** (obbligatorie per Azure Resource Manager e unità di test per la logica): spiegare cosa è necessario sapere al cliente per accedere a questo test drive e usarlo. Esaminare uno scenario per l'uso dell'offerta e esattamente ciò che il cliente deve conoscere per accedere alle funzionalità nell'test drive. In questo campo è possibile immettere fino a 10.000 caratteri di testo.

- **Manuale dell'utente** (obbligatorio): una procedura dettagliata approfondita dell'esperienza test drive. Il manuale dell'utente dovrebbe coprire esattamente ciò che si vuole che il cliente possa ottenere dall'esperienza del test drive e fungere da riferimento per eventuali domande. Il file deve essere in formato PDF ed essere denominato (255 caratteri al massimo) dopo il caricamento.

- **Video: aggiungere video** (facoltativo): è possibile caricare video in YouTube o Vimeo e farvi riferimento con un collegamento e un'immagine di anteprima (533 x 324 pixel), in modo che un cliente possa visualizzare una descrizione dettagliata delle informazioni che consentono di comprendere meglio il test drive, tra cui come usare correttamente le funzionalità dell'offerta e comprendere gli scenari in cui vengono evidenziati i vantaggi.
  - **Nome** (obbligatorio)
  - **URL (solo YouTube o Vimeo)** (obbligatorio)
  - **Anteprima (533 x 324px)** : il file di immagine deve essere in formato png.

Selezionare **Salva** dopo aver completato questi campi.
