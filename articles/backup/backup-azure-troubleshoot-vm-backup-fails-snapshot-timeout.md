<properties
   pageTitle="Errore di backup delle VM di Azure: non è stato possibile comunicare con l'agente della macchina virtuale per lo stato dello snapshot a causa del timeout della sottoattività di creazione snapshot della macchina virtuale | Microsoft Azure"
   description="Sintomi, cause e soluzioni per gli errori di backup delle macchine virtuali di Azure correlati a: Non è stato possibile comunicare con l'agente della macchina virtuale per lo stato dello snapshot. Si è verificato il timeout della sottoattività di creazione snapshot della macchina virtuale."
   services="backup"
   documentationCenter=""
   authors="genlin"
   manager="jwhit"
   editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/27/2016"
    ms.author="jimpark; markgal"/>

# Errore di backup delle VM di Azure: non è stato possibile comunicare con l'agente della macchina virtuale per lo stato dello snapshot a causa del timeout della sottoattività di creazione snapshot della macchina virtuale

## Riepilogo

Dopo la registrazione e la pianificazione di una macchina virtuale (VM) per il servizio Backup di Azure, il servizio avvia il processo di backup all'ora pianificata comunicando con l'estensione di backup nella macchina virtuale per acquisire uno snapshot temporizzato. Alcune condizioni potrebbero impedire l'attivazione dello snapshot e determinare un errore di backup. Questo articolo fornisce istruzioni per la risoluzione di problemi relativi a errori di backup di VM di Azure correlati all'errore di timeout dello snapshot.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Sintomo

Backup di Microsoft Azure per VM IaaS (infrastruttura distribuita come servizio) ha esito negativo e restituisce il messaggio di errore seguente nei dettagli errore processo nel portale di Azure.

**Non è stato possibile comunicare con l'agente della macchina virtuale per lo stato dello snapshot. Si è verificato il timeout della sottoattività di creazione snapshot della macchina virtuale.**

## Causa
Questo errore può essere dovuto a quattro cause comuni:

- La macchina virtuale non ha accesso a Internet.
- L'agente di macchine virtuali di Microsoft Azure installato nella VM Linux non è aggiornato.
- Non è possibile aggiornare o caricare l'estensione di backup.
- Non è possibile recuperare lo stato degli snapshot o non è possibile acquisire gli snapshot.

## Causa 1: la macchina virtuale non ha accesso a Internet
In base al requisito di distribuzione, la macchina virtuale non ha accesso a Internet o sono presenti restrizioni che impediscono l'accesso all'infrastruttura di Azure.

L'estensione di backup richiede che la connettività agli indirizzi IP pubblici di Azure funzioni correttamente, perché invia comandi a un endpoint di Archiviazione di Azure (URL HTTP) per la gestione degli snapshot della macchina virtuale. Se l'estensione non ha accesso a Internet pubblico, il backup ha esito negativo.

### Soluzione
In questo scenario usare uno dei metodi seguenti per risolvere il problema:

- Aggiungere gli intervalli IP dei data center di Azure all'elenco elementi consentiti.
- Creare un percorso per il flusso del traffico HTTP

### Per aggiungere gli intervalli IP dei data center di Azure all'elenco elementi consentiti

1. Ottenere l'elenco di [indirizzi IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653) da aggiungere all'elenco elementi consentiti.
2. Sbloccare gli indirizzi IP usando il cmdlet New-NetRoute. Eseguire questo cmdlet nella macchina virtuale di Azure, in una finestra di PowerShell con privilegi elevati, eseguita come amministratore.
3. Aggiungere regole al gruppo di sicurezza di rete, se presente, per consentire l'accesso agli indirizzi IP.

### Per creare un percorso per il flusso del traffico HTTP

1. Se sono presenti restrizioni di rete, ad esempio come gruppo di sicurezza di rete, distribuire un server proxy HTTP per instradare il traffico.
2. Se è presente un gruppo di sicurezza di rete (NSG), aggiungere regole per consentire l'accesso a Internet dal proxy HTTP.

Sono disponibili informazioni su come [configurare un proxy HTTP per i backup di macchine virtuali](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups).

## Causa 2: l'agente di macchine virtuali di Microsoft Azure installato nella VM Linux non è aggiornato

### Soluzione
La maggior parte degli errori relativi ad agenti o estensioni nelle macchine virtuali Linux è dovuta a problemi correlati ad agenti di macchine virtuali non aggiornati. In generale, per risolvere questo problema occorre prima di tutto seguire questa procedura:

1. [Installare la versione più recente dell'agente di macchine virtuali di Azure](https://acom-swtest-2.azurewebsites.net/documentation/articles/virtual-machines-linux-update-agent/).
2. Assicurarsi che l'agente di Azure sia in esecuzione nella macchina virtuale. A tale scopo, eseguire il comando seguente: ```ps -e```

    Se il processo non è in esecuzione, usare i comandi seguenti per riavviarlo.

    Per Ubuntu: ```service walinuxagent start```

    Per altre distribuzioni: ```service waagent start
```

3. [Configurare l'agente per il riavvio automatico](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).

4. Eseguire un nuovo backup di prova. Se l'errore persiste, raccogliere i log delle cartelle seguenti per altre operazioni di debug.

    È necessario acquisire i log seguenti dalla macchina virtuale del cliente:

    - /var/lib/waagent/*.xml
    - /var/log/waagent.log
    - /var/log/azure/*

Se è necessaria una registrazione dettagliata per waagent, seguire questa procedura per abilitarla:

1. Nel file /etc/waagent.conf trovare la riga seguente:

    Enable verbose logging (y|n)

2. Modificare il valore di **Logs.Verbose** da n a y.
3. Salvare la modifica e riavviare waagent seguendo la procedura precedente in questa sezione.

## Causa 3: non è possibile aggiornare o caricare l'estensione di backup
Se non è possibile caricare le estensioni, Backup ha esito negativo perché non è possibile acquisire uno snapshot.

### Soluzione
Per utenti guest di Windows:

1. Verificare che il servizio iaasvmprovider sia abilitato e abbia un tipo di avvio automatico.
2. Se la configurazione è diversa, abilitare il servizio per determinare se il backup successivo ha esito positivo.

Se l'aggiornamento o il caricamento dell'estensione di backup ancora non riesce, è possibile forzare il ricaricamento dell'estensione VMSnapshot installando l'estensione. L'estensione viene ricaricata al successivo tentativo di backup.

### Per disinstallare l'estensione

1. Accedere al [portale di Azure](https://ms.portal.azure.com/).
2. Trovare la macchina virtuale specifica che presenta problemi di backup.
3. Fare clic su **Impostazioni**.
4. Fare clic su **Estensioni**.
5. Fare clic su **Estensione VMSnapshot**.
6. Fare clic su **Disinstalla**.

In questo modo l'estensione viene reinstallata durante il backup successivo.

## Causa 4: non è possibile recuperare lo stato degli snapshot o non è possibile acquisire gli snapshot
Il backup delle macchine virtuali si basa sull'esecuzione del comando di snapshot sull'archiviazione sottostante. Il backup può non riuscire perché non ha accesso alla risorsa di archiviazione o a causa di un ritardo nell'esecuzione dell'attività di snapshot.

### Soluzione
Le condizioni seguenti possono causare errori dell'attività di snapshot:

| Causa | Soluzione |
| ----- | ----- |
| Macchine virtuali in cui è configurato il backup di Microsoft SQL Server. Per impostazione predefinita, il servizio Backup esegue backup VSS completi nelle macchine virtuali Windows. Nelle macchine virtuali che eseguono server basati su SQL Server e in cui è configurato il backup di SQL Server possono verificarsi ritardi nell'esecuzione di snapshot. | Se si verificano errori di backup a causa di problemi di snapshot, impostare la chiave del Registro di sistema seguente.<br><br>[HKEY\_LOCAL\_MACHINE\\SOFTWARE\\MICROSOFT\\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE" |
| Stato della macchina virtuale segnalato in modo non corretto perché la macchina virtuale viene arrestata in RDP. Se la macchina virtuale viene arrestata in RDP, verificare nel portale che lo stato della macchina virtuale sia indicato correttamente. | In caso contrario, arrestare la macchina virtuale nel portale tramite l'opzione "Spegni" nel dashboard della macchina virtuale. |
| Diverse macchine virtuali dello stesso servizio cloud sono configurate per eseguire il backup nello stesso momento. | È consigliabile distribuire le macchine virtuali dello stesso servizio cloud con pianificazioni di backup diverse. |
| L'esecuzione della macchina virtuale fa un uso elevato della CPU o della memoria. | Se l'esecuzione della macchina virtuale fa un uso elevato della CPU (oltre il 90%) o della memoria, l'attività di snapshot viene accodata e ritardata e infine si verifica il timeout. In una situazione di questo tipo, provare a eseguire un backup su richiesta. |
|La macchina virtuale non riesce a ottenere l'indirizzo dell'host/infrastruttura dal DHCP.|DHCP deve essere abilitato nel computer guest per consentire il funzionamento del backup delle VM IaaS. Se la macchina virtuale non riesce a ottenere l'indirizzo dell'host/infrastruttura dal DHCP, risposta 245, non è possibile scaricare o eseguire le estensioni. Se è necessario un indirizzo IP privato statico, è necessario configurarlo tramite la piattaforma. L'opzione DHCP all'interno della VM deve essere abilitata. Sono disponibili altre informazioni su [Come impostare un indirizzo IP privato interno statico](../virtual-network/virtual-networks-reserved-private-ip.md).|

<!---HONumber=AcomDC_0427_2016-->