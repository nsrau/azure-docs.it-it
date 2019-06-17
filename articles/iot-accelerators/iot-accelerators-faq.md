---
title: Domande frequenti sugli acceleratori di soluzioni IoT - Azure | Microsoft Docs
description: Domande frequenti sugli acceleratori di soluzioni IoT
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: b2f08e811217572e09a254e9ab3306ab954b14b5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61447957"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Domande frequenti sugli acceleratori di soluzioni IoT

Vedere anche le [Domande frequenti specifiche per Connected Factory](iot-accelerators-faq-cf.md) e le [Domande frequenti specifiche per il monitoraggio remoto](iot-accelerators-faq-rm-v2.md).

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Dove è possibile visualizzare il codice sorgente degli acceleratori di soluzioni?

Il codice sorgente è memorizzato nei repository di GitHub seguenti:

* [Acceleratore di soluzioni di monitoraggio remoto (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Acceleratore di soluzioni di monitoraggio remoto (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Acceleratore di soluzioni di manutenzione predittiva](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Acceleratore di soluzioni di connected factory](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Quali SDK è possibile usare per sviluppare i client di dispositivi per gli acceleratori di soluzioni?

È possibile trovare collegamenti agli SDK dei dispositivi IoT per i vari linguaggi (C, .NET, Java, Node.js, Python) nel repository GitHub [Microsoft Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks).

Se si usa il dispositivo DevKit, è possibile trovare risorse ed esempi nel repository GitHub [IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk).

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>La nuova architettura dei microservizi è disponibile per tutte e tre gli acceleratori di soluzioni?

Attualmente solo la soluzione di monitoraggio remoto usa l'architettura dei microservizi, in quanto è associata allo scenario più esteso.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Quali vantaggi offre la nuova architettura basata su microservizi open source nel nuovo aggiornamento?

Negli ultimi due anni l'architettura cloud si è evoluta notevolmente. I microservizi sono emersi come ottimo modello per ottenere scalabilità e flessibilità senza compromettere la velocità di sviluppo. Questo modello di architettura viene usato in diversi servizi Microsoft internamente, con ottimi risultati in termini di affidabilità e scalabilità. Microsoft sta mettendo in pratica queste conoscenze negli acceleratori di soluzioni in modo che i clienti possano trarne vantaggio.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Un amministratore del servizio vuole modificare il mapping della directory tra la sottoscrizione e un tenant di Azure AD specifico. Come completare questa attività

Vedere [Come aggiungere una sottoscrizione esistente alla directory di Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Si vuole modificare un amministratore del servizio o coamministratore quando si accede con un account dell'organizzazione

Vedere l'articolo di supporto [Modifica dell'amministratore del servizio e del coamministratore quando si effettua l'accesso con un account dell'organizzazione](https://azure.microsoft.com/support/changing-service-admin-and-co-admin).

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Perché viene visualizzato un errore simile al seguente? "L'account non ha le autorizzazioni appropriate per creare una soluzione. Rivolgersi all'amministratore account o provare con un account diverso".

Per indicazioni vedere il diagramma seguente:

![Diagramma di flusso delle autorizzazioni](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Se si continua a visualizzare l'errore dopo aver confermato di essere un amministratore globale del tenant Azure AD e un coamministratore della sottoscrizione, l'amministratore dell'account dovrà rimuovere l'utente e assegnare nuovamente le autorizzazioni necessarie in questo ordine. Innanzitutto, dovrà aggiungere l'utente come amministratore globale e quindi aggiungere l'utente come coamministratore della sottoscrizione di Azure. Se i problemi persistono, contattare [Guida e supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Perché viene visualizzato questo errore quando si dispone di una sottoscrizione di Azure? "Per creare soluzioni preconfigurate è necessaria una sottoscrizione di Azure. È possibile creare un account di valutazione gratuito in pochi minuti."

Se si è certi di avere una sottoscrizione di Azure, convalidare il mapping del tenant per la sottoscrizione e verificare che sia selezionato il tenant corretto nell'elenco a discesa. Se si è verificato che il tenant è corretto, seguire il diagramma precedente e verificare il mapping della sottoscrizione e il tenant di Azure AD.

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Dove è possibile reperire informazioni sulla versione precedente della soluzione di monitoraggio remoto?

La versione precedente dell'acceleratore di soluzione di monitoraggio remoto era nota come soluzione di monitoraggio remoto IoT Suite preconfigurata. È possibile trovare la documentazione archiviata all'indirizzo [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/).

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Il nuovo acceleratore di soluzioni è disponibile nelle stesse aree geografiche di quella esistente?

Sì, la nuova soluzione di monitoraggio remoto è disponibile nelle stesse aree geografiche.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Che cos'è la differenza tra l'eliminazione di un gruppo di risorse nel portale di Azure e fare clic per eliminare un solution accelerator in azureiotsolutions.com?

* Se si elimina l'acceleratore di soluzione in [azureiotsolutions.com](https://www.azureiotsolutions.com/), eliminare tutte le risorse che sono state distribuite durante la creazione dell'acceleratore di soluzione. Se sono state aggiunte altre risorse al gruppo, anche queste ultime vengono eliminate.
* Se si elimina il gruppo di risorse nel [portale di Azure](https://portal.azure.com), si eliminano solo le risorse presenti in tale gruppo. È anche necessario eliminare l'applicazione Azure Active Directory associata all'acceleratore di soluzioni.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>È possibile continuare a sfruttare gli investimenti negli acceleratori di soluzioni Azure IoT?

Sì. Qualsiasi soluzione esistente oggi continua a essere inclusa nella sottoscrizione di Azure e il codice sorgente rimane disponibile in GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Di quante istanze dell'hub IoT è possibile eseguire il provisioning in una sottoscrizione?

Per impostazione predefinita, è possibile eseguire il provisioning di [10 hub IoT per ogni sottoscrizione](../azure-subscription-service-limits.md#iot-hub-limits). È possibile creare un [ticket di supporto di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per aumentare questo limite. Di conseguenza, poiché ogni acceleratore di soluzioni effettua il provisioning di un nuovo hub IoT, è possibile effettuare il provisioning solo di un massimo di 10 acceleratori di soluzioni in una determinata sottoscrizione.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Di quante istanze di Azure Cosmos DB è possibile effettuare il provisioning in una sottoscrizione?

Cinquanta. Anche se è possibile creare un [ticket di supporto di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per aumentare questo limite, per impostazione predefinita è possibile effettuare il provisioning solo di 50 istanze di Cosmos DB per ogni sottoscrizione.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Di quante API di Bing Maps gratuite è possibile eseguire il provisioning in una sottoscrizione?

Due. È possibile creare solo due Transazioni sito Web interno - Livello 1 per Bing Maps per i piani aziendali in una sottoscrizione di Azure. Per impostazione predefinita, il provisioning della soluzione di monitoraggio remoto viene effettuato con il piano Transazioni interne di livello 1. Di conseguenza, è possibile eseguire il provisioning di un massimo di due soluzioni per il monitoraggio remoto in una sottoscrizione senza modifiche.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>È possibile creare un acceleratore di soluzioni se è disponibile Microsoft Azure per DreamSpark?

> [!NOTE]
> Microsoft Azure per DreamSpark è ora noto come Microsoft Imagine per studenti.

Attualmente non è possibile creare un acceleratore di soluzioni con un account [Microsoft Azure per DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/), ma in un paio di minuti è possibile creare un [account di valutazione gratuito per Azure](https://azure.microsoft.com/free/), che consente di creare un acceleratore di soluzioni.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Come si elimina un tenant di Azure AD?

Vedere il post del blog di Eric Golpe relativo alla [procedura dettagliata di eliminazione di un tenant di Azure AD](https://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Passaggi successivi

È anche possibile esplorare alcune altre funzionalità degli acceleratori di soluzioni IoT:

* [Esplorare le funzionalità dell'acceleratore di soluzioni di monitoraggio remoto](quickstart-remote-monitoring-deploy.md)
* [Panoramica dell'acceleratore di soluzioni di manutenzione predittiva](iot-accelerators-predictive-overview.md)
* [Distribuire l'acceleratore di soluzioni di connected factory](quickstart-connected-factory-deploy.md)
* [Sicurezza IoT sin dall'inizio](/azure/iot-fundamentals/iot-security-ground-up)
