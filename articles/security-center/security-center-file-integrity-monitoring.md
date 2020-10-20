---
title: Monitoraggio dell'integrità dei file nel centro sicurezza di Azure | Microsoft Docs
description: Informazioni su come configurare il monitoraggio dell'integrità dei file nel centro sicurezza di Azure usando questa procedura dettagliata.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 45e173f87559368244ab7b0f90af1dbb6fc739d4
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205388"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Monitoraggio dell'integrità dei file nel centro sicurezza di Azure
Informazioni su come configurare il monitoraggio dell'integrità dei file nel centro sicurezza di Azure usando questa procedura dettagliata.


## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Disponibile a livello generale|
|Prezzi:|Richiede [Azure Defender per i server](defender-for-servers-introduction.md).<br>Il monitoraggio dell'integrità dei file carica i dati nell'area di lavoro Log Analytics. Si applicano costi in base alla quantità di dati caricati. Per altre informazioni, vedere [Prezzi di Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).|
|Autorizzazioni e ruoli obbligatori:|Il **proprietario dell'area di lavoro** può abilitare/disabilitare FIM (per altre informazioni, vedere [ruoli di Azure per log Analytics](https://docs.microsoft.com/services-hub/health/azure-roles#azure-roles)).<br>Il **lettore** può visualizzare i risultati.|
|Cloud:|![Sì ](./media/icons/yes-icon.png) cloud commerciali<br>![Sì ](./media/icons/yes-icon.png) US gov<br>![Nessun ](./media/icons/no-icon.png) gov per la Cina, altro gov<br>Supportato solo nelle aree in cui è disponibile la soluzione di rilevamento modifiche di automazione di Azure.<br>Vedere [aree supportate per l'area di lavoro log Analytics collegata](../automation/how-to/region-mappings.md).<br>[Altre informazioni sul rilevamento delle modifiche](../automation/change-tracking/overview.md).|
|||

## <a name="what-is-fim-in-security-center"></a>Cos'è il monitoraggio dell'integrità dei file nel Centro sicurezza?
Il monitoraggio dell'integrità dei file, detto anche monitoraggio delle modifiche, esamina i file del sistema operativo, i registri di Windows, il software applicativo, i file di sistema Linux e altro ancora per le modifiche che potrebbero indicare un attacco. 

Il Centro sicurezza consiglia di eseguire il monitoraggio delle entità con FIM ed è anche possibile definire i propri criteri o entità FIM da monitorare. FIM segnala l'utente per attività sospette, ad esempio:

- Creazione o rimozione di chiavi di file e del registro di sistema
- Modifiche nei file (modifiche nelle dimensioni dei file, elenchi di controllo degli accessi e hash del contenuto)
- Modifiche nei registri di sistema (modifiche nelle dimensioni, elenchi di controllo degli accessi, tipi e contenuti)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Esaminare l'elenco delle entità suggerite da monitorare con FIM
> * Definire regole FIM personalizzate
> * Controllare le modifiche apportate alle entità monitorate
> * Usare i caratteri jolly per semplificare il rilevamento tra le directory


## <a name="how-does-fim-work"></a>Come funziona FIM?

Confrontando lo stato corrente di questi elementi con lo stato durante l'analisi precedente, FIM avvisa l'utente se sono state apportate modifiche sospette.

Il monitoraggio dell'integrità dei file usa la soluzione Rilevamento modifiche di Azure per tenere traccia delle modifiche nell'ambiente e identificarle. Quando il monitoraggio dell'integrità dei file è abilitato, si ha una risorsa **rilevamento modifiche** di tipo **soluzione**. Per informazioni dettagliate sulla frequenza di raccolta dati, vedere [rilevamento modifiche dettagli sulla raccolta dati](../automation/change-tracking/overview.md#change-tracking-and-inventory-data-collection).

> [!NOTE]
> Se si rimuove la risorsa di **rilevamento modifiche** , viene disabilitata anche la funzionalità di monitoraggio dell'integrità dei file nel centro sicurezza.

## <a name="which-files-should-i-monitor"></a>Quali file è necessario monitorare?
Quando si scelgono i file da monitorare, considerare quali sono i file critici per il sistema e le applicazioni. Consente di monitorare i file che non si prevede di modificare senza alcuna pianificazione. Se si scelgono i file modificati di frequente dalle applicazioni o dal sistema operativo, ad esempio file di log e file di testo, verranno creati molti rumori, rendendo difficile l'identificazione di un attacco.

Il Centro sicurezza fornisce l'elenco seguente di elementi consigliati da monitorare in base ai modelli di attacco noti.

|File Linux|File di Windows|Chiavi del registro di sistema di Windows (HKLM = HKEY_LOCAL_MACHINE)|
|:----|:----|:----|
|/bin/login|C:\autoexec.bat|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/bin/passwd|C:\boot.ini|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ 603BCC1F-4b59-4E08-B724-D2C6297EF351}|
|/etc/*. conf|C:\config.sys|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\IniFileMapping\SYSTEM.ini \boot|
|/usr/bin|C:\Windows\system.ini|CurrentVersion HKLM\SOFTWARE\Microsoft\Windows|
|/usr/sbin|C:\Windows\win.ini|NT\CurrentVersion\Winlogon HKLM\SOFTWARE\Microsoft\Windows|
|/bin|C:\Windows\regedit.exe|Cartelle HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell|
|/sbin|C:\Windows\System32\userinit.exe|Cartelle della shell di HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User|
|/Boot|C:\Windows\explorer.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run|
|/usr/local/bin|C:\Programmi\Microsoft Security Client\msseces.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce|
|/usr/local/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnceEx|
|/opt/bin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServices|
|/opt/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|/etc/crontab||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/etc/init.d||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ 603BCC1F-4b59-4E08-B724-D2C6297EF351}|
|/etc/cron.hourly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\IniFileMapping\system.ini \boot|
|/etc/cron.daily||CurrentVersion HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows|
|/etc/cron.weekly||NT\CurrentVersion\Winlogon HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows|
|/etc/cron.monthly||Cartelle HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\Shell|
|||Cartelle della shell di HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\User|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnce|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnceEx|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServices|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|||HKLM\SYSTEM\CurrentControlSet\Control\hivelist|
|||Manager\KnownDLLs HKLM\SYSTEM\CurrentControlSet\Control\Session|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\DomainProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\PublicProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\StandardProfile|


## <a name="enable-file-integrity-monitoring"></a>Abilita il monitoraggio dell'integrità dei file 

FIM è disponibile solo dalle pagine del Centro sicurezza nel portale di Azure. Attualmente non è disponibile alcuna API REST per l'uso di FIM.

1. Dall'area **protezione avanzata** del dashboard di **Azure Defender** selezionare **monitoraggio dell'integrità dei file**.

   :::image type="content" source="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png" alt-text="Avvio di FIM" lightbox="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png":::

    Verrà visualizzata la pagina configurazione **Monitoraggio integrità file** .

    Per ogni area di lavoro sono disponibili le informazioni seguenti:

    - Numero totale di modifiche che sono state effettuate nell'ultima settimana (potrebbe essere visualizzato un trattino "-" se il monitoraggio dell'integrità dei file non è abilitato nell'area di lavoro)
    - Numero totale di computer e macchine virtuali che creano report nell'area di lavoro
    - Posizione geografica dell'area di lavoro
    - Sottoscrizione di Azure in cui si trova l'area di lavoro

1. Usare questa pagina per:

    - Accedere e visualizzare lo stato e le impostazioni di ogni area di lavoro

    - ![Icona del piano ][4] di aggiornamento aggiornare l'area di lavoro per usare Azure Defender. Questa icona indica che l'area di lavoro o la sottoscrizione non è protetta da Azure Defender. Per usare le funzionalità FIM, la sottoscrizione deve essere protetta da Azure Defender. [Altre informazioni](security-center-pricing.md)

    - ![Icona Abilita][3] Abilitare FIM in tutti i computer nell'area di lavoro e configurare le opzioni FIM. Questa icona indica che FIM non è abilitato per l'area di lavoro.

        :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-list-fim.png" alt-text="Avvio di FIM":::


    > [!TIP]
    > Se non è disponibile alcun pulsante Abilita o aggiorna e lo spazio è vuoto, significa che FIM è già abilitato nell'area di lavoro.


1. Selezionare **Abilita**. Vengono visualizzati i dettagli dell'area di lavoro, incluso il numero di computer Windows e Linux nell'area di lavoro.

    :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-fim-status.png" alt-text="Avvio di FIM":::

   Sono elencate anche le impostazioni consigliate per Windows e Linux.  Espandere **File Windows**, **Registro di sistema** e **File Linux** per visualizzare l'elenco completo di elementi consigliati.

1. Deselezionare le caselle di controllo per tutte le entità consigliate che non si desidera vengano monitorate da FIM.

1. Selezionare **Applica il monitoraggio dell'integrità dei file** per abilitare il monitoraggio dell'integrità dei file.

> [!NOTE]
> È possibile modificare le impostazioni in qualsiasi momento. Per altre informazioni, vedere [Modificare le entità monitorate](#edit-monitored-entities) di seguito.



## <a name="audit-monitored-workspaces"></a>Controllare le aree di lavoro monitorate 

Viene visualizzato il dashboard **Monitoraggio dell'integrità dei file** per le aree di lavoro in cui è abilitata la funzionalità. Il dashboard FIM viene aperto dopo l'abilitazione di FIM in un'area di lavoro o quando si seleziona un'area di lavoro nella finestra **Monitoraggio integrità file** in cui è già abilitato FIM.

:::image type="content" source="./media/security-center-file-integrity-monitoring/fim-dashboard.png" alt-text="Avvio di FIM":::

Il dashboard FIM per un'area di lavoro Visualizza i dettagli seguenti:

- Numero totale di macchine virtuali connesse all'area di lavoro
- Numero totale di modifiche che sono state effettuate durante il periodo di tempo selezionato
- Suddivisione per tipo di modifica (file, registro di sistema)
- Suddivisione per categoria di modifica (modifica, aggiunta, rimozione)

Selezionare **filtro** nella parte superiore del dashboard per modificare il periodo di tempo per cui vengono visualizzate le modifiche.

:::image type="content" source="./media/security-center-file-integrity-monitoring/dashboard-filter.png" alt-text="Avvio di FIM":::

Nella scheda **Server** sono elencati i computer che inviano report a questa area di lavoro. Per ogni computer, il dashboard elenca:

- Il numero totale di modifiche che sono state effettuate durante il periodo di tempo selezionato
- Una suddivisione delle modifiche totali come modifiche al file o al registro di sistema

La **Ricerca log** si apre quando si immette un nome di computer nel campo di ricerca o si seleziona un computer elencato nella scheda computer. Ricerca log consente di visualizzare tutte le modifiche apportate durante il periodo di tempo selezionato per il computer. Per altre informazioni, è possibile espandere una modifica.

![Ricerca log][8]

La scheda **Modifiche** (mostrata sotto) elenca tutte le modifiche per l'area di lavoro durante il periodo di tempo selezionato. Per ogni entità modificata, il dashboard elenca:

- Il computer in cui si è verificata la modifica
- Il tipo di modifica (al registro di sistema o al file)
- La categoria di modifica (modifica, aggiunta o rimozione)
- Data e ora della modifica

![Le modifiche per l'area di lavoro][9]

**Dettagli modifiche** si apre quando si immette una modifica nel campo di ricerca oppure si seleziona un'entità elencata nella scheda **Modifiche**.

![Dettagli modifiche][10]

## <a name="edit-monitored-entities"></a>Modificare le entità monitorate

1. Tornare al **dashboard di monitoraggio dell'integrità dei file** e selezionare **Impostazioni**.

   ![Impostazioni][11]

   Si apre **Configurazione dell'area di lavoro**, che visualizza tre schede: **Registro di sistema di Windows**, **File Windows** e **File Linux**. Ogni scheda elenca le entità che è possibile modificare nella categoria. Per ogni entità elencata, il Centro sicurezza identifica se il monitoraggio dell'integrità dei file è abilitato (true) o non abilitato (false).  La modifica dell'entità consente di abilitare o disabilitare il monitoraggio dell'integrità dei file.

   ![Configurazione dell'area di lavoro][12]

2. Selezionare una protezione dell'identità. In questo esempio è stato selezionato un elemento in Registro di sistema di Windows. Si apre **Modifica Registro di sistema di Windows per Rilevamento modifiche**.

   ![Rilevamento delle modifiche][13]

In **Modifica Registro di sistema di Windows per Rilevamento modifiche** è possibile:

- Abilitare (True) o disabilitare (False) il monitoraggio dell'integrità dei file
- Specificare o modificare il nome dell'entità
- Specificare o modificare il valore o il percorso
- Eliminare l'entità, rimuovere o salvare la modifica

## <a name="add-a-new-entity-to-monitor"></a>Aggiungere una nuova entità da monitorare
1. Tornare al **dashboard Monitoraggio dell'integrità dei file** e selezionare **Impostazioni** nella parte superiore. Si apre **Configurazione dell'area di lavoro**.
2. In **Configurazione dell'area di lavoro** selezionare la scheda per il tipo di entità che si desidera aggiungere: Registro di sistema di Windows, File Windows o File Linux. In questo esempio è stato selezionato **File Linux**.

   ![Aggiungere una nuova entità da monitorare][14]

3. Selezionare **Aggiungi**. Si apre **Aggiungi file Linux per Rilevamento modifiche**.

   ![Immettere le informazioni richieste][15]

4. Nella pagina **Aggiungi** digitare le informazioni richieste e selezionare **Salva**.

## <a name="disable-monitored-entities"></a>Disabilitare le entità monitorate
1. Tornare al dashboard di **monitoraggio dell'integrità dei file** .
2. Selezionare un'area di lavoro in cui la funzionalità è attualmente abilitata. Un'area di lavoro è abilitata per il monitoraggio dell'integrità dei file se manca il pulsante Abilita o Piano di aggiornamento.

   ![Selezionare un'area di lavoro in cui la funzionalità è abilitata][16]

3. In Monitoraggio integrità file selezionare **Impostazioni**.

   ![Selezionare Impostazioni][17]

4. In **Configurazione dell'area di lavoro** selezionare un gruppo in cui **Abilitato** sia impostato su true.

   ![Configurazione dell'area di lavoro][18]

5. Nella finestra **Modifica Registro di sistema di Windows per Rilevamento modifiche** impostare **Abilitato** su False.

   ![Impostare Abilitato su False][19]

6. Selezionare **Salva**.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Monitoraggio di cartelle e percorsi con l'uso di caratteri jolly

Usare i caratteri jolly per semplificare le attività di rilevamento nelle directory. Quando viene configurato il monitoraggio delle cartelle con l'uso di caratteri jolly, si applicano le regole seguenti:
-   I caratteri jolly sono necessari per il rilevamento di più file.
-   I caratteri jolly possono essere usati solo nell'ultimo segmento di un percorso, as esempio C:\cartella\file o /etc/*.conf
-   Se una variabile di ambiente include un percorso non valido, la convalida avrà esito positivo ma il percorso avrà esito negativo quando si esegue l'inventario.
-   Quando si imposta il percorso, evitare di specificare percorsi generici, ad esempio c:\*.*, poiché sarà individuato un numero eccessivo di cartelle.

## <a name="disable-fim"></a>Disabilitare il monitoraggio dell'integrità dei file
È possibile disabilitare il monitoraggio dell'integrità dei file. Il monitoraggio dell'integrità dei file usa la soluzione Rilevamento modifiche di Azure per tenere traccia delle modifiche nell'ambiente e identificarle. La disabilitazione rimuove la soluzione Rilevamento modifiche dall'area di lavoro selezionata.

1. Per disabilitare FIM, tornare al dashboard di **monitoraggio dell'integrità dei file** .
2. Selezionare un'area di lavoro.
3. In **Monitoraggio integrità file**selezionare **Disabilita**.

   ![Disabilitare il monitoraggio dell'integrità dei file][20]

4. Selezionare **Rimuovi** per disabilitare.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come usare il monitoraggio dell'integrità dei file (FIM) nel centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere le pagine seguenti:

* [Impostazione dei criteri di sicurezza](tutorial-security-policy.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione dei consigli di sicurezza](security-center-recommendations.md): informazioni su come i consigli semplificano la protezione delle risorse di Azure.
* [Blog sulla sicurezza di Azure](https://docs.microsoft.com/archive/blogs/azuresecurity/): informazioni e notizie aggiornate sulla sicurezza di Azure.

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png