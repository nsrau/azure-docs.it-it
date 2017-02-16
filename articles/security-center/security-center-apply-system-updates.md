---
title: Applicare gli aggiornamenti del sistema nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento illustra come implementare le raccomandazioni **Applica gli aggiornamenti del sistema** e **Riavvia dopo gli aggiornamenti del sistema** del Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 51501cc5e344c321652982a3a7f448d84f892392


---
# <a name="apply-system-updates-in-azure-security-center"></a>Applicare gli aggiornamenti del sistema nel Centro sicurezza di Azure
Il Centro sicurezza di Azure monitora ogni giorno le macchine virtuali (VM) Windows e Linux alla ricerca di eventuali aggiornamenti mancanti del sistema operativo. Il Centro sicurezza recupera un elenco di aggiornamenti di sicurezza e critici disponibili da Windows Update o Windows Server Update Services (WSUS), in base al servizio configurato nella macchina virtuale Windows.  Il Centro sicurezza cerca anche gli aggiornamenti più recenti nei sistemi Linux. Se nella macchina virtuale non è stato applicato un aggiornamento del sistema, il Centro sicurezza ne consiglierà l'applicazione.

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio.  Questa non è una guida dettagliata.
> 
> 

## <a name="implement-the-recommendation"></a>Implementare la raccomandazione
1. Nel pannello **Consigli** selezionare **Applica gli aggiornamenti del sistema**.
   ![Applicare gli aggiornamenti di sistema][1]
2. Viene visualizzato il pannello **Applica gli aggiornamenti del sistema** , che mostra un elenco di macchine virtuali prive di alcuni aggiornamenti del sistema. Selezionare una macchina virtuale.
   ![Selezionare una macchina virtuale][2]
3. Viene visualizzato un pannello che mostra un elenco di aggiornamenti mancanti per la VM. Selezionare un aggiornamento del sistema. In questo esempio viene selezionato l'aggiornamento KB3156016.
   ![Aggiornamenti della sicurezza mancanti][3]
4. Seguire la procedura illustrata nel pannello **Aggiornamento per la sicurezza** per applicare l'aggiornamento mancante.
   ![Security update][4]

## <a name="reboot-after-system-updates"></a>Riavviare dopo gli aggiornamenti del sistema
1. Tornare al pannello **Raccomandazioni** . Dopo l'applicazione degli aggiornamenti del sistema è stata generata una nuova voce, denominata **Riavvia dopo gli aggiornamenti del sistema**. Questa voce indica che è necessario avviare la VM per completare il processo di applicazione degli aggiornamenti del sistema.
   ![Riavviare dopo gli aggiornamenti del sistema][5]
2. Selezionare **Riavvia dopo gli aggiornamenti del sistema**. Verrà visualizzato il pannello **In attesa di un riavvio per completare gli aggiornamenti del sistema** che mostra un elenco di macchine virtuali che devono essere riavviate per completare il processo di aggiornamento del sistema.
   ![Riavvio in sospeso][6]

Riavviare la VM da Azure per completare il processo.

## <a name="see-also"></a>Vedere anche
Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) : informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : post di blog sulla sicurezza e sulla conformità di Azure.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/recommendation.png
[2]:./media/security-center-apply-system-updates/select-vm.png
[3]: ./media/security-center-apply-system-updates/missing-security-updates.png
[4]: ./media/security-center-apply-system-updates/security-update.png
[5]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[6]: ./media/security-center-apply-system-updates/restart-pending.png



<!--HONumber=Nov16_HO3-->


