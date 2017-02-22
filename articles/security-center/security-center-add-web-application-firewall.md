---
title: Aggiungere un Web application firewall al Centro sicurezza di Azure | Documentazione Microsoft
description: Questo argomento illustra come implementare le raccomandazioni **Aggiungi un web application firewall** e **Finalizza la protezione dell&quot;applicazione** del Centro sicurezza di Azure.
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
ms.date: 12/01/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 2286437f4ab13384f895e906ccda48ac1b4c553d
ms.openlocfilehash: b44a0373ceca84b423984e01eee1e57a67d97cdd


---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Aggiungere un Web application firewall al Centro sicurezza di Azure
È possibile che il Centro sicurezza di Azure consigli di aggiungere un WAF (Web Application Firewall) di un partner Microsoft per proteggere le applicazioni Web. Questo documento contiene un esempio su come eseguire questa operazione.

Viene visualizzata una raccomandazione WAF per qualsiasi IP pubblico (IP a livello di istanza o IP con carico bilanciato) cui è associato un gruppo di sicurezza di rete con porte Web in ingresso aperte (80, 443).

Centro sicurezza consiglia di effettuare il provisioning di un WAF per consentire la protezione da attacchi contro le applicazioni Web nelle macchine virtuali e nell'Ambiente del servizio app. Un Ambiente del servizio app è un'opzione del piano di servizio [Premium](https://azure.microsoft.com/pricing/details/app-service/) del Servizio app di Azure che offre un ambiente completamente isolato e dedicato per eseguire in modo sicuro tutte le app del servizio. Per altre informazioni sull'ambiente del servizio app, vedere [Documentazione relativa agli ambienti del servizio app](../app-service/app-service-app-service-environments-readme.md).

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio.  Questo argomento non costituisce una guida dettagliata.
>
>

## <a name="implement-the-recommendation"></a>Implementare la raccomandazione
1. Nel pannello **Raccomandazioni** selezionare **Secure web application using web application firewall** (Proteggi l'applicazione Web usando web application firewall).
   ![Proteggere l'applicazione Web][1]
2. Nel pannello **Proteggere le applicazioni Web mediante Web application firewall** selezionare un'applicazione Web. Viene visualizzato il pannello **Aggiungi un web application firewall** .
   ![Add a web application firewall][2]
3. È possibile scegliere di usare un Web application firewall esistente eventualmente disponibile o di crearne uno nuovo. In questo esempio non sono disponibili WAF esistenti ed è pertanto necessario creare un WAF.
4. Per creare un WAF, selezionare una soluzione dall'elenco di partner integrati. In questo esempio viene selezionato **Barracuda Web Application Firewall**.
5. Viene visualizzato il pannello **Barracuda Web Application Firewall** , dove sono disponibili informazioni sulla soluzione del partner. Selezionare **Crea** nel pannello informativo.
   ![Pannello di informazioni sul firewall][3]
6. Viene visualizzato il pannello **Nuovo web application firewall**, in cui è possibile eseguire la procedura di **Configurazione macchina virtuale** e fornire i dati richiesti in **Informazioni sul web application firewall**. Selezionare **Configurazione macchina virtuale**.
7. Nel pannello **Configurazione macchina virtuale** immettere le informazioni necessarie per avviare la macchina virtuale che esegue il WAF.
   ![VM configuration][4]
8. Tornare al pannello **Nuovo web application firewall** e selezionare **Informazioni sul web application firewall**. Nel pannello **WAF Information** (Informazioni WAF) è possibile configurare il WAF. Il passaggio 7 consente di configurare la macchina virtuale che esegue il WAF, mentre nel passaggio 8 si esegue il provisioning del WAF stesso.

## <a name="finalize-application-protection"></a>Finalizza la protezione dell'applicazione
1. Tornare al pannello **Raccomandazioni** . Dopo la creazione del WAF viene aggiunta una nuova voce: **Finalizza la protezione dell'applicazione**. Questa raccomandazione informa l'utente che è necessario completare il processo di connessione effettiva del WAF all'interno della rete virtuale di Azure in modo da proteggere l'applicazione.
   ![Finalizza la protezione dell'applicazione][5]
2. Selezionare **Finalizza la protezione dell'applicazione**. Viene visualizzato un nuovo pannello. Come si può vedere, è presente un'applicazione Web il cui traffico deve essere reindirizzato.
3. Selezionare l'applicazione Web. Viene visualizzato un pannello in cui è possibile eseguire la procedura per finalizzare la configurazione del Web application firewall. Completare i passaggi e quindi selezionare **Limita il traffico**. Centro sicurezza completa l'operazione.
   ![Limita il traffico][6]

> [!NOTE]
> Per proteggere più applicazioni Web in Centro sicurezza, è possibile aggiungerle alle distribuzioni WAF esistenti.
>
>

I log generati dal WAF sono ora completamente integrati. Il Centro sicurezza può avviare automaticamente la raccolta e l'analisi dei log, per mettere in evidenza eventuali avvisi di sicurezza importanti.

## <a name="see-also"></a>Vedere anche
In questo documento è stato illustrato come implementare la raccomandazione "Aggiungere un Web application firewall". Per altre informazioni sulla configurazione di un Web application firewall, vedere gli argomenti seguenti:

* [Configurazione di un Web application firewall (WAF) per l'ambiente del servizio app](../app-service-web/app-service-app-service-environment-web-application-firewall.md)

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



<!--HONumber=Feb17_HO3-->


