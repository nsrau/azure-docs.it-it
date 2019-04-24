---
title: Rilevamento delle minacce nel Centro sicurezza di Azure | Microsoft Docs
description: " Rilevare minacce e attività dannose grazie all'integrazione di Microsoft Cloud App Security con il Centro sicurezza di Azure. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: 7ffb9684045031c5bca7a79a15db7cb16fc99e9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60332398"
---
# <a name="ueba-for-azure-resources-and-users"></a>UEBA per risorse e utenti di Azure 

Il Centro sicurezza di Azure collabora con Microsoft Cloud App Security per fornire avvisi basati su UEBA (User and Entity Behavioral Analytics) per le risorse e gli utenti di Azure (attività di Azure). Questi avvisi rilevano anomalie nel comportamento dell'utente e sono basati su funzionalità UEBA e di apprendimento automatico che consentono di eseguire immediatamente il rilevamento avanzato delle minacce nelle attività delle sottoscrizioni. Poiché sono abilitate automaticamente, le nuove funzionalità di rilevamento delle anomalie offrono risultati immediati fornendo rilevamento istantaneo di numerose anomalie comportamentali degli utenti e delle risorse associate alla sottoscrizione. Inoltre, questi avvisi sfruttano altri dati già esistenti nel motore di rilevamento di Microsoft Cloud App Security per velocizzare il processo di analisi e contenere le minacce continue. 

> [!NOTE]
> Il Centro sicurezza di Azure può archiviare una copia dei dati di sicurezza del cliente raccolti da o associati a una risorsa del cliente (ad esempio, una macchina virtuale o un tenant di Azure Active Directory) in due posizioni: (a) Nella stessa area geografica in cui si trova tale risorsa, tranne le aree geografiche in cui Microsoft non ha ancora provveduto a distribuire il Centro sicurezza di Azure, nel qual caso una copia di tali dati verrà archiviata negli Stati Uniti. (b) Qualora il Centro sicurezza di Azure usi un altro servizio online Microsoft per elaborare i dati di sicurezza, questi possono essere archiviati in conformità alle regole di georilevazione dell'altro servizio online.
>

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Prerequisiti

- Una [licenza di Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security) valida e attivata
- [Livello Standard del Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/)
 
## <a name="threat-detection-alerts"></a>Avvisi di rilevamento delle minacce

Il Centro sicurezza supporta gli avvisi di rilevamento delle anomalie di Cloud App Security, ad esempio:

**Comunicazione impossibile**
-  Questo rilevamento identifica due attività utente (in una singola sessione o in più sessioni) provenienti da località geograficamente distanti e realizzate in un intervallo di tempo più breve rispetto a quello che sarebbe stato necessario all'utente per spostarsi dalla prima alla seconda località. Si tratta pertanto di un altro utente che sta usando le stesse credenziali. Questo rilevamento sfrutta un algoritmo di apprendimento automatico che ignora i "falsi positivi" evidenti che contribuiscono alla condizione di comunicazione impossibile, ad esempio le VPN e le località usate regolarmente da altri utenti dell'organizzazione. Questo rilevamento prevede un periodo di apprendimento iniziale di sette giorni durante il quale apprende lo schema di attività di un nuovo di utente.

**Attività da un paese non frequente**
- Questo rilevamento prende in considerazione le località di attività passate per determinare località nuove e non frequenti. Il motore di rilevamento delle anomalie archivia le informazioni sulle località precedenti usate dagli utenti dell'organizzazione. Quando un'attività viene eseguita in una località che non è stata recentemente o mai visitata dagli utenti dell'organizzazione, viene attivato un avviso. 

**Attività da indirizzi IP anonimi**
- Questo rilevamento indica che erano presenti utenti attivi da un indirizzo IP identificato come indirizzo IP proxy anonimo. Questi proxy vengono usati da persone che vogliono nascondere l'indirizzo IP del dispositivo e possono essere usati per attacchi dannosi. Questo rilevamento sfrutta un algoritmo di apprendimento automatico che riduce i "falsi positivi", ad esempio gli indirizzi IP contrassegnati in modo errato che vengono usati diffusamente dagli utenti dell'organizzazione.
 
  ![avviso di rilevamento delle minacce](./media/security-center-ueba-mcas/security-center-mcas-alert.png)

## <a name="disabling-threat-detection-alerts"></a>Disabilitazione degli avvisi di rilevamento delle minacce

Questi avvisi sono abilitati per impostazione predefinita, ma è possibile disabilitarli:

1. Nel pannello del Centro sicurezza selezionare **Criteri di sicurezza**. Per la sottoscrizione che si vuole modificare fare clic su **Modifica impostazioni**.
2.  Fare clic su **Rilevamento delle minacce**.
3. In **Enable integrations** (Abilita integrazioni) deselezionare **Allow Microsoft Cloud App Security to access my data** (Consenti a Cloud App Security di accedere ai miei dati) e fare clic su **Salva**.

   ![avviso di rilevamento delle minacce](./media/security-center-ueba-mcas/security-center-mcas-optout.png)

> [!NOTE]
> È previsto un periodo di apprendimento iniziale di sette giorni durante i quali non tutti gli avvisi di rilevamento delle anomalie vengono generati. Successivamente, ogni sessione viene confrontata con l'attività, l'intervallo di tempo in cui gli utenti sono stati attivi, gli indirizzi IP e i dispositivi rilevati nel mese trascorso e con il punteggio di rischio di queste attività. Questi rilevamenti fanno parte del motore di rilevamento delle anomalie basato sull'apprendimento automatico. Questo motore profila l'ambiente e attiva gli avvisi rispetto a una baseline appresa sull'attività dell'organizzazione. I rilevamenti sfruttano anche gli algoritmi di apprendimento automatico studiati per profilare gli utenti e i criteri di accesso per ridurre i falsi positivi.
>
  
## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come usare la funzionalità di rilevamento delle minacce nel Centro sicurezza di Azure. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
