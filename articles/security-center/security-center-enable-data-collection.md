---
title: Abilitare la raccolta dati nel Centro sicurezza di Azure | Documentazione Microsoft
description: " Informazioni su come abilitare la raccolta dati nel Centro sicurezza di Azure. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 48742fdbd1335ae519e4537ccd3779dc4f120a4d


---
# <a name="enable-data-collection-in-azure-security-center"></a>Abilitare la raccolta dati nel Centro sicurezza di Azure
Per consentire ai clienti a impedire, rilevare e rispondere alle minacce, il Centro sicurezza di Azure raccoglie ed elabora dati sulle macchine virtuali di Azure, incluse le informazioni di configurazione, i metadati, i registri eventi e altro. La prima volta che si accede al Centro sicurezza, la raccolta dati viene abilitata in tutte le macchine virtuali della sottoscrizione. La raccolta dati è consigliata, ma è possibile rifiutare esplicitamente disattivandola nei criteri del Centro sicurezza. Vedere [Disabilitazione della raccolta dati](#disabling-data-collection). Se si disattiva la raccolta dati, il Centro sicurezza consiglierà di attivare la raccolta dati nei criteri di sicurezza per la sottoscrizione in questione.

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio. Questa non è una guida dettagliata.
> 
> 

## <a name="implement-the-recommendation"></a>Implementare la raccomandazione
1. Selezionare il riquadro **Raccomandazioni** del pannello **Centro sicurezza**.  Verrà visualizzato il pannello **Raccomandazioni** .
   ![Pannello Centro sicurezza][1]
2. Nel pannello **Raccomandazioni** selezionare **Abilita la raccolta di dati per le sottoscrizioni**.  Verrà visualizzato il pannello **Attiva la raccolta di dati** .
   ![Pannello Raccomandazioni][2]
3. Nel pannello **Attiva la raccolta di dati** selezionare la sottoscrizione. Verrà visualizzato il pannello **Criteri di sicurezza** per la sottoscrizione.
4. Nel pannello **Criteri di sicurezza** selezionare **Sì** in **Raccolta di dati** per raccogliere automaticamente i log. Attivando la raccolta dati verrà anche eseguito il provisioning dell'estensione di monitoraggio per tutte le VM correnti e nuove supportate nella sottoscrizione.
   ![Pannello Criteri di sicurezza][3]
5. Selezionare **Salva**.
6. Selezionare **Scegliere un account di archiviazione per area**. Per ciascuna area in cui si dispone di macchine virtuali in esecuzione, è necessario selezionare l'account di archiviazione in cui vengono archiviati i dati raccolti da tali macchine virtuali. Se non si sceglie un account di archiviazione per ogni area, verrà creato automaticamente. In questo esempio verrà creato **newstoracct**. È possibile modificare l'account di archiviazione in un secondo momento tornando ai criteri di sicurezza della sottoscrizione e scegliendo un account di archiviazione diverso.
   ![Scegliere un account di archiviazione][4]
7. Selezionare **OK**.

> [!NOTE]
> È consigliabile attivare la raccolta dati e scegliere prima un account di archiviazione a livello di sottoscrizione. I criteri di sicurezza possono essere impostati a livello di sottoscrizione di Azure e a livello di gruppo di risorse, ma la configurazione della raccolta dati e dell'account di archiviazione viene eseguita solo a livello di sottoscrizione.
> 
> 

## <a name="after-data-collection-is-enabled"></a>Dopo aver abilitato la raccolta dati
La raccolta dei dati viene abilitata tramite l'agente di monitoraggio di Azure e l'estensione per il monitoraggio della sicurezza di Azure. L'estensione per il monitoraggio della sicurezza di Azure esegue l'analisi delle varie configurazioni relative alla sicurezza e le invia alle tracce di [Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Il sistema operativo crea anche le voci del registro eventi. L'agente di monitoraggio di Azure legge le voci del registro eventi ed ETW le traccia e le copia nell'account di archiviazione per l'analisi. L'agente di monitoraggio copia anche i file di dump di arresto anomalo nell'account di archiviazione. Si tratta dell'account di archiviazione configurato in Criteri di sicurezza.

## <a name="disabling-data-collection"></a>Disabilitazione della raccolta dati
È possibile disabilitare la raccolta dati in qualsiasi momento; gli agenti di monitoraggio installati dal Centro sicurezza verranno rimossi.  È necessario selezionare una sottoscrizione per disattivare la raccolta dati.

> [!NOTE]
> I criteri di sicurezza possono essere impostati a livello di sottoscrizione di Azure e a livello di gruppo di risorse, ma è necessario selezionare una sottoscrizione per disattivare la raccolta dei dati.
> 
> 

1. Tornare al pannello **Centro sicurezza** e selezionare il riquadro **Criteri**. Verrà visualizzato il pannello **Criteri di sicurezza - Definire i criteri per sottoscrizione o gruppo di risorse** .
   ![Selezionare il riquadro Criteri][5]
2. Nel pannello **Criteri di sicurezza - Definire i criteri per sottoscrizione o gruppo di risorse** selezionare la sottoscrizione per la quale disabilitare la raccolta dati.
   ![Selezionare la sottoscrizione per disabilitare la raccolta dati][6]
3. Verrà visualizzato il pannello **Criteri di sicurezza** per la sottoscrizione.  Selezionare **No** in Raccolta di dati.
4. Fare clic su **Salva** nella barra multifunzione in alto.
5. Selezionare l'opzione **Elimina agenti** nella barra multifunzione in alto per rimuovere gli agenti dalle macchine virtuali esistenti.

## <a name="see-also"></a>Vedere anche
Questo documento illustra come implementare la raccomandazione "Abilita raccolta dati" del Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) : informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): informazioni e notizie aggiornate sulla sicurezza di Azure.

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/security-center-blade.png
[2]: ./media/security-center-enable-data-collection/recommendations.png
[3]: ./media/security-center-enable-data-collection/data-collection.png
[4]: ./media/security-center-enable-data-collection/storage-account.png
[5]: ./media/security-center-enable-data-collection/policy.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png



<!--HONumber=Dec16_HO2-->


