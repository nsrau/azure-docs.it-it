---
title: Aggiungere un Web application firewall al Centro sicurezza di Azure | Documentazione Microsoft
description: Questo argomento illustra come implementare le raccomandazioni **Aggiungi un web application firewall** e **Finalizza la protezione dell'applicazione** del Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 8f56139a-4466-48ac-90fb-86d002cf8242
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2018
ms.author: terrylan
ms.openlocfilehash: 4454d18893d698e49f118048eca0bfc94df315a5
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Aggiungere un Web application firewall al Centro sicurezza di Azure
È possibile che il Centro sicurezza di Azure consigli di aggiungere un WAF (Web Application Firewall) di un partner Microsoft per proteggere le applicazioni Web. Questo documento contiene un esempio su come eseguire questa operazione.

Viene visualizzata una raccomandazione WAF per qualsiasi IP pubblico (IP a livello di istanza o IP con carico bilanciato) cui è associato un gruppo di sicurezza di rete con porte Web in ingresso aperte (80, 443).

Il Centro sicurezza consiglia di effettuare il provisioning di un WAF per consentire la protezione da attacchi contro le applicazioni Web nelle macchine virtuali e negli Ambienti del servizio app esterni. Un Ambiente del servizio app è un'opzione del piano di servizio [Premium](https://azure.microsoft.com/pricing/details/app-service/) del Servizio app di Azure che offre un ambiente completamente isolato e dedicato per eseguire in modo sicuro tutte le app del servizio. Per altre informazioni sull'ambiente del servizio app, vedere [Documentazione relativa agli ambienti del servizio app](../app-service/environment/intro.md).

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio.  Questo argomento non costituisce una guida dettagliata.
>
>

## <a name="implement-the-recommendation"></a>Implementare la raccomandazione
1. In **Raccomandazioni** selezionare **Secure web application using web application firewall** (Proteggi l'applicazione Web usando web application firewall).
   ![Proteggere l'applicazione Web][1]
2. In **Secure your web applications using web application firewall** (Proteggi le applicazioni Web usando web application firewall) selezionare un'applicazione Web. Si aprirà la finestra **Aggiungi un web application firewall**.
   ![Aggiungere un Web Application Firewall][2]
3. È possibile scegliere di usare un Web application firewall esistente eventualmente disponibile o di crearne uno nuovo. In questo esempio non sono disponibili WAF esistenti ed è pertanto necessario creare un WAF.
4. Per creare un WAF, selezionare una soluzione dall'elenco di partner integrati. In questo esempio viene selezionato **Barracuda Web Application Firewall**.
5. Si aprirà la finestra **Barracuda Web Application Firewall** con informazioni sulla soluzione del partner. Selezionare **Create**.

   ![Pannello di informazioni sul firewall][3]

6. Si aprirà la finestra **Nuovo web application firewall**, in cui è possibile eseguire la procedura di **Configurazione macchina virtuale** e fornire i dati richiesti in **Informazioni sul web application firewall**. Selezionare **Configurazione macchina virtuale**.
7. In **Configurazione macchina virtuale** immettere le informazioni necessarie per avviare la macchina virtuale che esegue il WAF.
   ![VM configuration][4]
8. Tornare a **Nuovo web application firewall** e selezionare **Informazioni sul web application firewall**. In **Informazioni sul web application firewall** è possibile configurare il WAF. Il passaggio 7 consente di configurare la macchina virtuale che esegue il WAF, mentre nel passaggio 8 si esegue il provisioning del WAF stesso.

## <a name="finalize-application-protection"></a>Finalizza la protezione dell'applicazione
1. Tornare a **Raccomandazioni**. Dopo la creazione del WAF viene aggiunta una nuova voce: **Finalizza la protezione dell'applicazione**. Questa raccomandazione informa l'utente che è necessario completare il processo di connessione effettiva del WAF all'interno della rete virtuale di Azure in modo da proteggere l'applicazione.

   ![Finalizza la protezione dell'applicazione][5]

2. Selezionare **Finalizza la protezione dell'applicazione**. Viene visualizzato un nuovo pannello. Come si può vedere, è presente un'applicazione Web il cui traffico deve essere reindirizzato.
3. Selezionare l'applicazione Web. Viene visualizzato un pannello in cui è possibile eseguire la procedura per finalizzare la configurazione del Web application firewall. Completare i passaggi e quindi selezionare **Limita il traffico**. Centro sicurezza completa l'operazione.

   ![Limita il traffico][6]

> [!NOTE]
> Per proteggere più applicazioni Web in Centro sicurezza, è possibile aggiungerle alle distribuzioni WAF esistenti.
>
>

I log generati dal WAF sono ora completamente integrati. Il Centro sicurezza può avviare automaticamente la raccolta e l'analisi dei log, per mettere in evidenza eventuali avvisi di sicurezza importanti.

## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato illustrato come implementare la raccomandazione "Aggiungere un Web application firewall". Per altre informazioni sulla configurazione di un Web application firewall, vedere gli argomenti seguenti:

* [Configurazione di un Web application firewall (WAF) per l'ambiente del servizio app](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) : informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : post di blog sulla sicurezza e sulla conformità di Azure.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
