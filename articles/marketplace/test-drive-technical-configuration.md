---
title: Configurazione tecnica test drive, Marketplace commerciale Microsoft
description: Informazioni sulle unità di test. I test drive consentono ai nuovi clienti di test drive l'offerta prima di effettuare il commit all'acquisto.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/13/2019
author: keferna
ms.author: keferna
ms.openlocfilehash: 7d22226721d4fc09b4f5affc15047b6799ed0d19
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409477"
---
# <a name="test-drive-technical-configuration"></a>Configurazione tecnica del test drive

L'opzione test drive in Microsoft Commercial Marketplace consente di configurare una panoramica pratica e autonoma delle funzionalità principali del prodotto. Con una test drive, i nuovi clienti possono provare l'offerta prima di eseguire il commit per acquistarla. Per altre informazioni, vedere [Informazioni sul test drive](what-is-test-drive.md).

Se non si desidera più fornire un test drive per l'offerta, tornare alla pagina **impostazione offerta** e deselezionare la casella di controllo **Abilita Test Drive** . Non tutti i tipi di offerta hanno un test drive disponibile.

## <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager test drive

Questa è l'unica opzione test drive per le offerte di macchine virtuali o app di Azure e richiede anche un'installazione abbastanza dettagliata. Leggere le sezioni seguenti per [i dettagli della sottoscrizione di distribuzione](#deployment-subscription-details) e gli [elenchi di test drive](#test-drive-listings), quindi continuare con l'argomento separato per la [configurazione di Azure Resource Manager Test Drive](azure-resource-manager-test-drive.md).

## <a name="hosted-test-drive"></a>test drive ospitata

Microsoft può rimuovere gli elementi di complessità della configurazione di un test drive ospitando e gestendo il provisioning e la distribuzione del servizio con questo tipo di test drive. La configurazione di questo tipo di test drive ospitato è la stessa, indipendentemente dal fatto che la test drive sia destinata a un pubblico di Dynamics 365 business Central, Dynamics 365 Customer Engagement o a Dynamics 365 Operations.

- **Max test drive simultanee** (obbligatorio): impostare il numero massimo di clienti per volta che possono usare il test drive. Ogni utente simultaneo utilizzerà una licenza Dynamics 365 mentre il test drive è attivo, quindi assicurarsi di disporre di un numero sufficiente di licenze disponibili per supportare il limite massimo impostato. Il valore consigliato è 3-5.

- **Durata test drive** (obbligatorio): immettere il numero di ore per cui il test drive resterà attivo (almeno un'ora). Dopo questo periodo, la sessione terminerà e non utilizzerà più una delle licenze. È consigliabile immettere un valore di 2-24 ore, a seconda della complessità dell'offerta. La durata può essere impostata solo in periodi di ore intere (ad esempio, "2" è un valore valido, a differenza di "1,5"). L'utente può richiedere una nuova sessione se esaurisce il tempo e vuole accedere di nuovo al test drive.

- **URL istanza** (obbligatorio): l'URL in cui il cliente avvierà un test drive. È in genere l'URL dell'istanza di Dynamics 365 che esegue l'app con i dati di esempio installati, ad esempio `https://testdrive.crm.dynamics.com`.

- **URL dell'API Web dell'istanza** (obbligatorio): recuperare l'URL dell'API Web per l'istanza di Dynamics 365 accedendo all'account Microsoft 365 e passando a **Impostazioni**  >  **personalizzazione**  >  **risorse per sviluppatori**  >  **istanza Web API (URL radice del servizio)**, copiare l'URL trovato qui (ad esempio, `https://testdrive.crm.dynamics.com/api/data/v9.0` ).

- **Nome ruolo** (obbligatorio): specificare il nome del ruolo di sicurezza definito nel test drive di Dynamics 365 personalizzato, che verrà assegnato all'utente durante il test drive (ad esempio, test-drive-role).

Per informazioni su come configurare l'ambiente Dynamics 365 per test drive e concedere l'autorizzazione AppSource per il provisioning e il deprovisioning di test drive utenti nel tenant, seguire [queste istruzioni](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md).

## <a name="logic-app-test-drive"></a>test drive app per la logica

Questo tipo di test drive non è ospitato da Microsoft. È possibile usarlo per connettersi a un'offerta Dynamics 365 o ad altre risorse personalizzate, che include un'ampia gamma di architetture di soluzioni complesse. Per altre informazioni sulla configurazione di test drive per app per la logica, vedere [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) (Operazioni) e [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) (Coinvolgimento del cliente) su GitHub.

- **Area** (obbligatorio, elenco a discesa a selezione singola): attualmente esistono 26 aree supportate da Azure in cui è possibile rendere disponibile il test drive. Le risorse per l'app per la logica verranno distribuite nell'area selezionata. Se alcune risorse personalizzate dell'app per la logica sono archiviate in un'area specifica, assicurarsi che l'area sia selezionata qui. Il modo migliore per farlo è distribuire completamente l'app per la logica in locale nella sottoscrizione di Azure nel portale e verificare che funzioni prima di effettuare questa selezione.

- **Max test drive simultanee** (obbligatorio): impostare il numero massimo di clienti per volta che possono usare il test drive. Questi test drive sono già distribuiti e consentono ai clienti di accedervi istantaneamente senza attendere una distribuzione.

- **Durata test drive** (obbligatorio): immettere il numero di ore per cui il test drive resterà attivo (almeno un'ora). Il test drive termina automaticamente alla fine di questo periodo di tempo.

- **Nome del gruppo di risorse di Azure** (obbligatorio): immettere il nome del [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md#resource-groups)in cui viene salvata l'App per la logica test drive.

- **Nome dell'app per la logica** (obbligatorio): immettere il nome dell'app per la logica che assegna il test drive all'utente. Questa app per la logica deve essere salvata nel gruppo di risorse di Azure precedente.

- **Deprovisioning app per la logica nome** (obbligatorio): immettere il nome dell'app per la logica che annulla il provisioning del test drive al termine del cliente. Questa app per la logica deve essere salvata nel gruppo di risorse di Azure precedente.

## <a name="power-bi-test-drive"></a>Test drive di Power BI

I prodotti che vogliono fornire una dimostrazione di un oggetto visivo di Power BI interattivo possono usare un collegamento incorporato per condividere un dashboard personalizzato come test drive, senza che siano necessarie altre attività di configurazione tecnica. È sufficiente caricare l'URL di Power BI incorporato.

Per altre informazioni sulla configurazione di Power BI app, vedere [che cosa sono le app Power bi?](https://docs.microsoft.com/power-bi/service-template-apps-overview)

## <a name="deployment-subscription-details"></a>Dettagli di sottoscrizione della distribuzione

Per consentire a Microsoft di distribuire il test automaticamente, creare e specificare una sottoscrizione di Azure separata e univoca (non obbligatoria per i test drive di Power BI).

- **ID sottoscrizione di Azure** (obbligatorio per Azure Resource Manager e app per la logica): immettere l'ID sottoscrizione per concedere l'accesso ai servizi dell'account Azure per la fatturazione e la creazione di report sull'utilizzo delle risorse. È consigliabile provare a [creare una sottoscrizione di Azure separata](../cost-management-billing/manage/create-subscription.md) da usare per i test drive, se non esiste già. Per trovare gli ID sottoscrizione di Azure, accedere al [portale di Azure](https://portal.azure.com/) e passare alla scheda **Sottoscrizioni** del menu a sinistra. Se si seleziona la scheda, viene visualizzato l'ID sottoscrizione, ad esempio "a83645ac-1234-5ab6-6789-1h234g764ghty".

- **Azure ad ID tenant** (obbligatorio): immettere l' [id tenant](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)di Azure Active Directory (ad). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare **Proprietà** e quindi cercare il numero **ID directory** elencato, ad esempio 50c464d3-4930-494c-963c-1e951d15360e. È anche possibile cercare l'ID tenant dell'organizzazione usando l'indirizzo del nome di dominio alla pagina [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **Nome tenant di Azure AD** (obbligatorio per Dynamics 365): immettere il nome di Azure Active Directory (AD). Per trovare questo nome, accedere al [portale di Azure](https://portal.azure.com/): nell'angolo in alto a destra il nome del tenant verrà elencato sotto il nome dell'account.

- **Azure ad ID app** (obbligatorio): immettere l' [id applicazione](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)di Azure Active Directory (ad). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare **registrazioni app**, quindi cercare il numero di **ID dell'applicazione** elencato (ad esempio `50c464d3-4930-494c-963c-1e951d15360e` ).

- **Azure ad segreto client App** (obbligatorio): immettere il [segreto client](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)dell'applicazione Azure ad. Per trovare questo valore, accedere al [portale di Azure](https://portal.azure.com/). Selezionare la scheda **Azure Active Directory** nel menu a sinistra, selezionare **Registrazioni app** e quindi selezionare l'app test drive. Selezionare **Certificati e segreti** e **Nuovo segreto client**, immettere una descrizione, selezionare **Mai** in **Scade** e quindi scegliere **Aggiungi**. Assicurarsi di copiare il valore. Non uscire dalla pagina prima di copiare il valore.

## <a name="test-drive-listings"></a>Elenchi di test drive

L'opzione per gli **elenchi di test drive** disponibile nella scheda **test drive** del centro per i partner Visualizza le lingue (e i mercati) in cui è disponibile il test drive, attualmente l'inglese (Stati Uniti) è l'unico percorso disponibile. Inoltre, in questa pagina vengono visualizzati lo stato della presentazione specifica della lingua e la data/ora in cui è stata aggiunta l'offerta. È necessario definire i dettagli del test drive (descrizione, manuale dell'utente, video e così via) per ogni lingua/mercato.

- **Descrizione** (obbligatoria): descrivere la test drive, gli elementi che verranno illustrati, gli obiettivi dell'utente per sperimentare, le funzionalità da esplorare e tutte le informazioni rilevanti per aiutare l'utente a determinare se acquisire l'offerta. In questo campo è possibile immettere fino a 3000 caratteri di testo.

- **Informazioni di accesso** (obbligatorie per Azure Resource Manager e unità di test per la logica): spiegare cosa è necessario sapere al cliente per accedere a questo test drive e usarlo. Descrivere dettagliatamente uno scenario per l'uso dell'offerta e fornire esattamente tutte le informazioni necessarie al cliente per accedere alle funzionalità durante il test drive. In questo campo è possibile immettere fino a 10.000 caratteri di testo.

- **Manuale dell'utente** (obbligatorio): una procedura dettagliata approfondita dell'esperienza test drive. Il manuale dell'utente deve trattare tutti gli aspetti che si vogliono far sperimentare al cliente con il test drive e deve fungere da riferimento per qualsiasi domanda del cliente. Il file deve essere in formato pdf e il nome deve essere assegnato (lunghezza massima 255 caratteri) dopo il caricamento.

- **Video: aggiungere video** (facoltativo): è possibile fare riferimento ai video ospitati altrove con un collegamento e un'immagine di anteprima (533 x 324 pixel), in modo che un cliente possa visualizzare una panoramica delle informazioni che consentono di comprendere meglio le test drive, tra cui come usare correttamente le funzionalità dell'offerta e comprendere gli scenari in cui vengono evidenziati i vantaggi.
  - **Nome** (obbligatorio)
  - **URL** (solo YouTube o Vimeo; obbligatorio)
  - **Anteprima** (533 x 324 pixel): l'immagine deve essere in formato png.

Se si sta creando il test drive nel centro per i partner, selezionare **Salva bozza** prima di continuare.

## <a name="next-step"></a>Passaggio successivo

- [Aggiornare un'offerta esistente nel marketplace commerciale](partner-center-portal/update-existing-offer.md)
