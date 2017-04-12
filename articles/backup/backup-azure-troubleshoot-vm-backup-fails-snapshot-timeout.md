---
title: "Risolvere i problemi relativi all&quot;errore di Backup di Azure: &quot;Si è verificato il timeout della sottoattività di creazione snapshot della macchina virtuale&quot; | Documentazione Microsoft"
description: "Sintomi, cause e risoluzioni degli errori di Backup di Azure correlati all&quot;errore: Non è stato possibile comunicare con l&quot;agente della macchina virtuale per lo stato dello snapshot. Si è verificato il timeout della sottoattività di creazione snapshot della macchina virtuale"
services: backup
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: genli;markgal;
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d7924d8aade1ea582faa0f319f8c1d16d5461fbc
ms.lasthandoff: 04/03/2017

---

# <a name="troubleshoot-azure-backup-failure-snapshot-vm-sub-task-timed-out"></a>Risolvere i problemi relativi all'errore di Backup di Azure: "Si è verificato il timeout della sottoattività di creazione snapshot della macchina virtuale"
## <a name="summary"></a>Riepilogo
Dopo la registrazione e la pianificazione di una macchina virtuale per il servizio Backup di Azure, tale servizio avvia il processo comunicando con l'estensione di backup della macchina virtuale per la creazione di uno snapshot temporizzato. Quattro condizioni possono impedire l'attivazione dello snapshot, che a sua volta può provocare l'errore di Backup. Questo articolo fornisce la procedura di risoluzione dei problemi per correggere gli errori di Backup correlati ai problemi di timeout dello snapshot.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Sintomo
Si verifica un errore di Backup di Azure per una macchina virtuale IaaS (Infrastructure as a Service), che restituisce il messaggio di errore seguente nei dettagli degli errori del processo nel [portale di Azure](https://portal.azure.com/): "Non è stato possibile comunicare con l'agente della macchina virtuale per lo stato dello snapshot. Si è verificato il timeout della sottoattività di creazione snapshot della macchina virtuale".

## <a name="cause-1-the-vm-has-no-internet-access"></a>Causa 1: la macchina virtuale non ha accesso a Internet
In base al requisito di distribuzione, la macchina virtuale non ha accesso a Internet o sono presenti restrizioni che impediscono l'accesso all'infrastruttura di Azure.

Per funzionare correttamente, l'estensione di backup richiede la connettività agli indirizzi IP pubblici di Azure. L'estensione invia comandi a un endpoint di Archiviazione di Azure (URL HTTP) per gestire gli snapshot della macchina virtuale. Se l'estensione non ha accesso a Internet pubblico, il servizio Backup ha esito negativo.

### <a name="solution"></a>Soluzione
Per risolvere il problema, provare ad applicare uno dei metodi seguenti.
#### <a name="allow-access-to-the-azure-datacenter-ip-ranges"></a>Consentire l'accesso agli intervalli di indirizzi IP del data center di Azure

1. Ottenere l'[elenco di indirizzi IP del data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653) a cui consentire l'accesso.
2. Sbloccare gli indirizzi IP eseguendo il cmdlet **New-NetRoute** nella macchina virtuale di Azure in una finestra di PowerShell con privilegi elevati. Eseguire il cmdlet come amministratore.
3. Per consentire l'accesso agli indirizzi IP, aggiungere regole al gruppo di sicurezza di rete, se disponibile.

#### <a name="create-a-path-for-http-traffic-to-flow"></a>Creare un percorso per il flusso del traffico HTTP

1. Se sono state applicate restrizioni di rete, ad esempio un gruppo di sicurezza di rete, distribuire un server proxy HTTP per indirizzare il traffico.
2. Per consentire l'accesso a Internet dal server proxy HTTP, aggiungere regole al gruppo di sicurezza di rete, se disponibile.

Per informazioni su come configurare un proxy HTTP per i backup delle macchine virtuali, vedere [Preparare l'ambiente per il backup di macchine virtuali di Azure](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups).

## <a name="cause-2-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Causa 2: l'agente installato nella VM Linux non è aggiornato

### <a name="solution"></a>Soluzione
La maggior parte degli errori relativi ad agenti o estensioni nelle macchine virtuali Linux è dovuta a problemi correlati ad agenti di macchine virtuali non aggiornati. Per risolvere il problema, seguire queste indicazioni generali:

1. Seguire le istruzioni per l'[aggiornamento dell'agente di macchine virtuali Linux](../virtual-machines/linux/update-agent.md).

 >[!NOTE]
 >È *fortemente consigliato* aggiornare l'agente solo tramite un repository di distribuzione. Non è consigliabile scaricare il codice dell'agente direttamente da GitHub e aggiornarlo. Se l'agente più recente non è disponibile per la distribuzione, contattare il supporto per la distribuzione per istruzioni su come installarlo. Per cercare l'agente più recente, passare alla pagina dell'[agente Linux di Microsoft Azure](https://github.com/Azure/WALinuxAgent/releases) nel repository GitHub.

2. Assicurarsi che l'agente di Azure sia in esecuzione nella macchina virtuale eseguendo il comando seguente: `ps -e`

 Se il processo non è in esecuzione, riavviarlo usando i comandi seguenti:

 * Per Ubuntu: `service walinuxagent start`
 * Per altre distribuzioni: `service waagent start`

3. [Configurare l'agente per il riavvio automatico](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Eseguire un nuovo backup di prova. Se l'errore persiste, raccogliere i log seguenti dalla macchina virtuale del cliente:

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

Se è necessaria una registrazione dettagliata per waagent, seguire questa procedura:

1. Nel file /etc/waagent.conf individuare la riga seguente: **Enable verbose logging (y|n)**
2. Modificare il valore di **Logs.Verbose** da *n* a *y*.
3. Salvare la modifica e riavviare waagent seguendo la procedura precedente in questa sezione.

## <a name="cause-3-the-backup-extension-fails-to-update-or-load"></a>Causa 3: non è possibile aggiornare o caricare l'estensione di backup
Se non è possibile caricare le estensioni, Backup ha esito negativo perché non è possibile acquisire uno snapshot.

### <a name="solution"></a>Soluzione

Per utenti guest di Windows:

Verificare che il servizio iaasvmprovider sia abilitato e abbia un tipo di avvio *automatico*. Se il servizio non è configurato in questo modo, abilitarlo per determinare se il backup successivo avrà esito positivo.

Per utenti guest di Linux:

La versione più recente di VMSnapshot per Linux (estensione usata da Backup) è 1.0.91.0.

Se l'aggiornamento o il caricamento dell'estensione di backup ancora non riesce, è possibile forzare il ricaricamento dell'estensione VMSnapshot disinstallandola. L'estensione viene ricaricata al successivo tentativo di backup.

Per disinstallare l'estensione, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Trovare la macchina virtuale che presenta problemi di backup.
3. Fare clic su **Impostazioni**.
4. Fare clic su **Estensioni**.
5. Fare clic su **Estensione Vmsnapshot**.
6. Fare clic su **Disinstalla**.  

Questa procedura reinstalla l'estensione durante il backup successivo.

## <a name="cause-4-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Causa 4: non è possibile recuperare lo stato degli snapshot o non è possibile acquisire uno snapshot
Il backup delle macchine virtuali si basa sull'esecuzione del comando di snapshot sull'account di archiviazione sottostante. Il backup può avere esito negativo perché non ha accesso all'account di archiviazione o perché l'esecuzione dell'attività dello snapshot è stata posticipata.

### <a name="solution"></a>Soluzione
Le condizioni seguenti possono causare errori dell'attività di snapshot:

| Causa | Soluzione |
| --- | --- |
| Per la macchina virtuale è stato configurato il backup di SQL Server. | Per impostazione predefinita, il servizio Backup delle macchine virtuali esegue backup VSS completi nelle macchine virtuali Windows. Nelle macchine virtuali che eseguono server basati su SQL Server e in cui è configurato il backup di SQL Server possono verificarsi ritardi nell'esecuzione di snapshot.<br><br>Se si verificano errori di backup a causa di problemi di snapshot, configurare la chiave seguente del Registro di sistema:<br><br>**[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE"** |
| Lo stato della macchina virtuale viene segnalato in modo non corretto perché la macchina virtuale viene arrestata in RDP. | Se si arresta la macchina virtuale in Remote Desktop Protocol (RDP), controllare il portale per determinare se lo stato della macchina virtuale è corretto. In caso contrario, arrestare la macchina virtuale nel portale tramite l'opzione **Spegni** nel dashboard della macchina virtuale. |
| Diverse macchine virtuali dello stesso servizio cloud sono configurate per eseguire il backup nello stesso momento. | È consigliabile distribuire le pianificazione dei backup per le macchine virtuali dello stesso servizio cloud. |
| L'esecuzione della macchina virtuale fa un uso elevato della CPU o della memoria. | Se l'esecuzione della macchina virtuale fa un uso elevato della CPU (oltre il 90%) o della memoria, l'attività di snapshot viene accodata e ritardata e infine si verifica il timeout. In una situazione di questo tipo, provare a eseguire un backup su richiesta. |
| La macchina virtuale non riesce a ottenere l'indirizzo dell'host/infrastruttura dal DHCP. | DHCP deve essere abilitato nel computer guest per consentire il funzionamento del backup delle VM IaaS.  Se la macchina virtuale non riesce a ottenere l'indirizzo dell'host/infrastruttura dal DHCP, risposta 245, non è possibile scaricare o eseguire le estensioni. Se è necessario un indirizzo IP privato statico, è necessario configurarlo tramite la piattaforma. L'opzione DHCP all'interno della VM deve essere abilitata. Per altre informazioni, vedere [Impostazione di un indirizzo IP privato interno statico](../virtual-network/virtual-networks-reserved-private-ip.md). |

