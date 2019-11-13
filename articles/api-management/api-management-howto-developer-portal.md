---
title: Panoramica del portale per sviluppatori di gestione API di Azure-gestione API di Azure | Microsoft Docs
description: Informazioni sul portale per sviluppatori in gestione API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 6bf8c8690977ef1036c853d8c1c01a3a366b50df
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74011488"
---
# <a name="azure-api-management-developer-portal-overview"></a>Panoramica del portale per sviluppatori di gestione API di Azure

Il portale per sviluppatori è un sito Web completamente personalizzabile e completamente personalizzabile con la documentazione delle API. Si tratta del punto in cui i consumer di API possono individuare le API, informazioni su come usarle, richiedere l'accesso e provarle.

Questo articolo descrive le differenze tra le versioni Self-Hosted e quelle gestite del portale per sviluppatori in gestione API. Viene inoltre illustrata l'architettura e vengono fornite le risposte alle domande più frequenti.

> [!WARNING]
> Il nuovo portale per sviluppatori è attualmente in fase di implementazione nei servizi gestione API.
> Se il servizio è stato appena creato o è un servizio di livello Developer, è necessario avere già la versione più recente. In caso contrario, è possibile che si verifichino problemi, ad esempio con la funzionalità di pubblicazione. L'implementazione della funzionalità dovrebbe essere completata entro il venerdì 22 novembre 2019.
>
> [Informazioni su come eseguire la migrazione dalla versione di anteprima alla versione disponibile a livello generale](#preview-to-ga) del portale per sviluppatori.

![Portale per sviluppatori di Gestione API](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-vs-self-hosted"></a>Versioni gestite e self-hosted

È possibile creare il portale per sviluppatori in due modi:

- **Versione gestita** : la modifica e la personalizzazione del portale, che è incorporata nell'istanza di gestione API ed è accessibile tramite l'URL `<your-api-management-instance-name>.developer.azure-api.net`. Per informazioni su come accedere e personalizzare il portale gestito, vedere [questo articolo della documentazione](api-management-howto-developer-portal-customize.md) .
- **Versione self-hosted** : tramite la distribuzione e l'hosting automatico del portale all'esterno di un'istanza di gestione API. Questo approccio consente di modificare la codebase del portale ed estendere la funzionalità di base fornita. È anche necessario aggiornare il portale alla versione più recente. Per informazioni dettagliate e istruzioni, vedere il [repository GitHub con il codice sorgente del portale][1]. L' [esercitazione per la versione gestita](api-management-howto-developer-portal-customize.md) scorre il pannello amministrativo del portale, disponibile anche nella versione self-hosted.

## <a name="portal-architectural-concepts"></a>Concetti relativi all'architettura del portale

I componenti del portale possono essere divisi logicamente in due categorie: *codice* e *contenuto*.

Il *codice* viene mantenuto nel [repository GitHub][1] e include:

- Widget, che rappresentano elementi visivi e combinano HTML, JavaScript, funzionalità di stile, impostazioni e mapping del contenuto. Gli esempi sono un'immagine, un paragrafo di testo, un modulo, un elenco di API e così via.
- Definizione dello stile, che specifica il modo in cui i widget possono essere con stile
- Motore, che genera pagine Web statiche dal contenuto del portale ed è scritto in JavaScript
- Editor visivo, che consente la personalizzazione e la creazione in browser

Il *contenuto* è suddiviso in due sottocategorie: contenuto del *portale* e *contenuto di gestione API*.

Il *contenuto del portale* è specifico del portale e include:

- Pagine, ad esempio pagina di destinazione, esercitazioni sulle API, post di Blog
- Immagini multimediali, animazioni e altro contenuto basato su file
- Layout: modelli, che corrispondono a un URL e definiscono la modalità di visualizzazione delle pagine
- Stili-valori per le definizioni di stile, ad esempio tipi di carattere, colori e bordi
- Impostazioni-configurazione, ad esempio favicon, metadati del sito Web

Il *contenuto del portale*, ad eccezione dei supporti, è espresso come documenti JSON.

Il *contenuto di gestione API* include entità quali API, operazioni, prodotti e sottoscrizioni.

Il portale è basato su un fork adattato del [Framework Paperbits](https://paperbits.io/). La funzionalità Paperbits originale è stata estesa per fornire widget specifici di gestione API (ad esempio, un elenco di API, un elenco di prodotti) e un connettore al servizio gestione API per il salvataggio e il recupero del contenuto.

## <a name="faq"></a>Domande frequenti

In questa sezione vengono riportate le risposte alle domande comuni sul nuovo portale per sviluppatori, che sono di natura generale. Per domande specifiche per la versione self-hosted, vedere [la sezione wiki del repository GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="a-idpreview-to-ga-how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/> come è possibile eseguire la migrazione dalla versione di anteprima del portale?

Usando la versione di anteprima del portale per sviluppatori, è stato effettuato il provisioning del contenuto di anteprima nel servizio gestione API. Il contenuto predefinito è stato modificato in modo significativo nella versione disponibile a livello generale per migliorare l'esperienza utente. Include anche nuovi widget.

Se si usa la versione gestita, reimpostare il contenuto del portale facendo clic su **Reimposta contenuto** nella sezione del menu **operazioni** . Se si conferma questa operazione, verrà rimosso tutto il contenuto del portale ed eseguito il provisioning del nuovo contenuto predefinito. Il motore del portale è stato aggiornato automaticamente nel servizio gestione API.

![Reimposta contenuto portale](media/api-management-howto-developer-portal/reset-content.png)

Se si usa la versione self-hosted, usare il `scripts/cleanup.bat` e `scripts/generate.bat` dal repository GitHub per rimuovere il contenuto esistente ed effettuare il provisioning di nuovo contenuto. Assicurarsi di aggiornare il codice del portale alla versione più recente dal repository GitHub in anticipo.

Se non si vuole reimpostare il contenuto del portale, è possibile prendere in considerazione l'uso di widget appena disponibili in tutte le pagine. I widget esistenti sono stati aggiornati automaticamente alle versioni più recenti.

Se il provisioning del portale è stato eseguito dopo l'annuncio della disponibilità generale, dovrebbe già presentare il nuovo contenuto predefinito. Non è richiesta alcuna azione da parte dell'utente.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-new-developer-portal"></a>Come è possibile eseguire la migrazione dal portale per sviluppatori precedente al nuovo portale per sviluppatori?

I portali sono incompatibili ed è necessario eseguire la migrazione manuale del contenuto.

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>Il nuovo portale dispone di tutte le funzionalità del vecchio portale?

Il nuovo portale per sviluppatori non supporta *applicazioni* e *problemi*. Se sono stati usati *problemi* nel portale precedente e ne sono necessari in una nuova, pubblicare un commento in [un problema di GitHub dedicato](https://github.com/Azure/api-management-developer-portal/issues/122).

### <a name="has-the-old-portal-been-deprecated"></a>Il portale precedente è stato deprecato?

I portali Developer e Publisher precedenti sono ora funzionalità *legacy* , che riceveranno solo gli aggiornamenti della sicurezza. Le nuove funzionalità verranno implementate solo nel nuovo portale per sviluppatori.

La deprecazione dei portali legacy verrà annunciata separatamente. In caso di domande, problemi o commenti, è possibile generarli [in un problema dedicato di GitHub](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="how-can-i-automate-portal-deployments"></a>Come è possibile automatizzare le distribuzioni del portale?

È possibile accedere a livello di codice e gestire il contenuto del portale per sviluppatori tramite l'API REST, indipendentemente dal fatto che si usi una versione gestita o self-hosted.

L'API è documentata nella [sezione wiki del repository GitHub][2]. Può anche essere usato per automatizzare le migrazioni di contenuto del portale tra ambienti, ad esempio da un ambiente di test all'ambiente di produzione. Altre informazioni su questo processo sono disponibili [in questo articolo della documentazione](https://aka.ms/apimdocs/migrateportal) su GitHub.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Il portale supporta i modelli di Azure Resource Manager e/o è compatibile con gestione API DevOps Resource Kit?

No.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>È necessario abilitare la connettività VNET aggiuntiva per le dipendenze del portale gestito?

No.

### <a name="im-getting-a-cors-error-when-using-the-interactive-console-what-should-i-do"></a>Viene ricevuto un errore CORS quando si usa la console interattiva. Cosa devo fare?

La console interattiva esegue una richiesta API sul lato client dal browser. È possibile risolvere il problema CORS aggiungendo [un criterio CORS](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS) sulle API. È possibile specificare tutti i parametri manualmente o usare i valori dei caratteri jolly `*`. Ad esempio:

```XML
<cors>
    <allowed-origins>
        <origin>*</origin>
    </allowed-origins>
    <allowed-methods>
        <method>GET</method>
        <method>POST</method>
        <method>PUT</method>
        <method>DELETE</method>
        <method>HEAD</method>
        <method>OPTIONS</method>
        <method>PATCH</method>
        <method>TRACE</method>
    </allowed-methods>
    <allowed-headers>
        <header>*</header>
    </allowed-headers>
    <expose-headers>
        <header>*</header>
    </expose-headers>
</cors>
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul nuovo portale per sviluppatori:

- [Accedere e personalizzare il portale per sviluppatori gestiti](api-management-howto-developer-portal-customize.md)
- [Configurare la versione self-hosted del portale][2]

Esplora altre risorse:

- [Repository GitHub con il codice sorgente][1]
- [Roadmap pubblica del progetto][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects