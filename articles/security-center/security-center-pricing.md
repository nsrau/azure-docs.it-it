---
title: Prezzi del Centro sicurezza | Microsoft Docs
description: Questo articolo fornisce informazioni sui prezzi del Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: fd4603d7bcbd1d243c6dd37d810040ff0ee1b3ae
ms.openlocfilehash: e61a9fad0c241da30a615e03aa18c6fbb03baf35


---
# <a name="azure-security-center-pricing"></a>Prezzi del Centro sicurezza di Azure
Il Centro sicurezza PC di Azure impedisce, rileva e risponde alle minacce mediante visibilità e controllo avanzati della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni di Azure, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

## <a name="pricing-tiers"></a>Piani tariffari
Il Centro sicurezza è disponibile in due livelli:

* Il **livello gratuito** è attivato automaticamente per tutte le sottoscrizioni di Azure. Il livello gratuito permette di conoscere lo stato di protezione delle risorse di Azure, i criteri di sicurezza di base, i consigli sulla sicurezza e l'aspetto di integrazione con i prodotti e i servizi dei partner.
* Il **livello standard** aggiunge funzionalità avanzate per il rilevamento delle minacce, tra cui business intelligence, analisi del comportamento, rilevamento delle anomalie, eventi imprevisti per la sicurezza e report di valutazione delle minacce. Per il livello standard è disponibile una **versione di valutazione gratuita di&90; giorni**.

Per altre informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/) del Centro sicurezza.

> [!NOTE]
> Il Centro sicurezza usa Archiviazione di Azure per salvare i dati sulla sicurezza generati dai nodi protetti. I costi associati a questa risorsa di archiviazione non sono incluse nel prezzo del servizio e vengono addebitati separatamente secondo le normali [tariffe di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/). I costi di archiviazione si applicano anche alla versione di valutazione.
>
>

## <a name="try-standard-free-for-90-days"></a>Prova gratuita del livello standard per 90 giorni
Per il livello standard è disponibile una versione di valutazione gratuita valida 90 giorni. Per avere la versione di valutazione gratuita del livello standard, selezionare il riquadro **Policy** (Criteri) del pannello **Centro sicurezza**. Selezionare la sottoscrizione che si desidera aggiornare al livello Standard. Nel pannello **Security policy** (Criteri di sicurezza) selezionare **Pricing tier** (Piano tariffario). Nel pannello **Scegliere un piano tariffario** selezionare **Standard - Versione di valutazione gratuita**.

![Versione di prova gratuita][1]

Al termine dei 90 giorni, se si sceglie di continuare a usare il servizio, l'uso verrà addebitato automaticamente.

## <a name="upgrade-to-standard"></a>Aggiornamento alla versione standard
Eseguire l'aggiornamento al livello standard per aggiungere il rilevamento avanzato delle minacce. Per ottenere il livello standard, fare clic sul riquadro **Criterio** nel pannello **Centro sicurezza**. Selezionare la sottoscrizione che si desidera aggiornare al livello Standard. Nel pannello **Security policy** (Criteri di sicurezza) selezionare **Pricing tier** (Piano tariffario). Nel pannello **Scegliere un piano tariffario** selezionare **Standard**.

![Livello Standard][2]

## <a name="why-upgrade-to-standard"></a>Perché eseguire l'aggiornamento al livello standard?
Il livello standard del Centro sicurezza fornisce tutte le funzionalità del livello gratuito, con in più il rilevamento avanzato. Il rilevamento avanzato delle minacce consente di identificare le minacce attive dirette alle risorse di Azure, fornendo le informazioni necessarie per rispondervi rapidamente.

Il Centro sicurezza si avvale di analisi della sicurezza avanzate, che vanno ben oltre gli approcci basati sulle firme. Le ultime innovazioni sul piano delle tecnologie per i Big Data e l'apprendimento automatico vengono usate per valutare gli eventi in tutta l'infrastruttura cloud, rilevando minacce impossibili da identificare con approcci manuali e prevedendo l'evoluzione degli attacchi.

Il livello standard fornisce le analisi di sicurezza seguenti:

* **Intelligence per le minacce**: vengono cercate le minacce note usando un'intelligence globale sulle minacce dei prodotti e servizi Microsoft, della Microsoft Digital Crimes Unit, del Microsoft Security Response Center e dei feed esterni
* **Analisi del comportamento**: vengono applicati schemi noti per individuare comportamenti dannosi
* **Rilevamento di anomalie**: viene usata la profilatura statistica per creare una baseline cronologica. Questa funzione segnala le deviazioni dalle baseline definite che corrispondono a vettori di potenziali attacchi

Nel pannello **Avvisi di sicurezza** illustrato di seguito il Centro sicurezza ha rilevato un **evento imprevisto** per la sicurezza. Con la definizione di evento imprevisto per la sicurezza si intende un'aggregazione di tutti gli avvisi relativi a una risorsa corrispondenti a modelli kill chain. Selezionando l'evento imprevisto per la sicurezza, compaiono altri dettagli sull'evento imprevisto, insieme a un elenco degli avvisi correlati. Selezionando l'avviso è possibile avere altre sull'evento imprevisto in questione.

![Evento imprevisto della sicurezza][3]

L'avviso sulla **comunicazione di rete** qui di seguito fornisce dettagli sull'evento imprevisto. Le informazioni includono una descrizione completa del problema, la gravità, lo stato (che in questo caso viene ignorato, su decisione dell'utente), la risorsa attaccata e le contromisure. È inoltre disponibile un elenco di collegamenti ai report di Microsoft con informazioni sulle minacce, utili per risolvere i problemi di sicurezza e per attuare contromisure di protezione.

![Dettagli dell'avviso di sicurezza][4]

## <a name="enable-data-collection"></a>Abilitare la raccolta di dati
Per abilitare l'analisi del comportamento delle macchine virtuali, occorre attivare la raccolta dei dati. Potrebbe essere necessario abilitare la raccolta dei dati al primo accesso al Centro sicurezza o quando si creano criteri di sicurezza.

Per controllare che sia abilitata la raccolta dei dati, selezionare il riquadro **Criterio**. Si apre il pannello **Criteri di sicurezza**, con elencate le sottoscrizioni Azure. Selezionare una sottoscrizione. Se la **Raccolta dei dati** è disattivata, attivarla e salvare la modifica. Vedere [Abilitare la raccolta dati nel Centro sicurezza di Azure](security-center-enable-data-collection.md).

## <a name="next-steps"></a>Passaggi successivi
* In questo documento sono stati presentati i prezzi del Centro sicurezza. Per altre informazioni sui prezzi, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/) del Centro sicurezza.
* Per saperne di più sulle funzionalità di rilevamento avanzate del Centro sicurezza, vedere [Funzionalità di rilevamento del Centro sicurezza di Azure](security-center-detection-capabilities.md).
* In caso di domande sull'uso del Centro sicurezza, vedere [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md).
* Se si hanno ancora domande sull'uso del Centro sicurezza o di Azure, visitare i [forum di Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureSecurityCenter&filter=alltypes&sort=lastpostdesc).

<!--Image references-->
[1]: ./media/security-center-pricing/free-trial.png
[2]: ./media/security-center-pricing/standard.png
[3]: ./media/security-center-pricing/incident.png
[4]: ./media/security-center-pricing/network-alert.png



<!--HONumber=Feb17_HO1-->


