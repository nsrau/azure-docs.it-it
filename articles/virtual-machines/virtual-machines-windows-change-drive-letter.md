<properties 
	pageTitle="Come modificare la lettera di unità del disco temporaneo di Windows" 
	description="Descrive come modificare un mapping del disco temporaneo in una macchina virtuale Windows in Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="kathydav"/>

#Come modificare la lettera di unità del disco temporaneo di Windows

Se si desidera usare l'unità D per archiviare i dati, seguire le istruzioni seguenti per usare un'unità diversa per il disco temporaneo. Non usare mai l'unità temporanea per archiviare i dati che si intende conservare.

Prima di iniziare è necessario disporre di un disco dati collegato alla macchina virtuale da usare per l'archiviazione del file di paging di Windows (pagefile.sys) durante la procedura. Se non si dispone di un disco dati collegato, vedere [Come collegare un disco dati a una macchina virtuale Windows][Attach]. Per istruzioni su come individuare i dischi collegati, vedere "Gestire i dischi" in [Informazioni sui dischi delle macchine virtuali in Azure][Disks].

Se si desidera usare un disco dati esistente nell'unità D, assicurarsi di avere caricato anche il disco rigido virtuale nell'account di archiviazione. Per le istruzioni, vedere i passaggi 3 e 4 in [Creare e caricare un disco rigido virtuale di Windows Server in Azure][VHD].

> [AZURE.WARNING]Se si ridimensiona una macchina virtuale e questa viene di conseguenza spostata in un host diverso, l'unità D diventa nuovamente l'unità temporanea.

##Modificare la lettera di unità

1. Accedere alla macchina virtuale. Per informazioni dettagliate, vedere [Come accedere a una macchina virtuale che esegue Windows Server][Logon].

2. Spostare pagefile.sys dall'unità D a un'altra unità.

3. Riavviare la macchina virtuale.

4. Eseguire di nuovo l'accesso e modificare la lettera di unità D in E.

5. Dal [portale di gestione di Azure](http://manage.windowsazure.com) collegare un disco dati esistente o un disco dati vuoto.

6.	Accedere di nuovo alla macchina virtuale, inizializzare il disco e assegnare la lettera di unità D per il disco appena collegato.

7.	Verificare che l'unità E sia stata mappata al disco di archiviazione temporanea.

8.	Spostare pagefile.sys dall'altra unità all'unità E.

## Risorse aggiuntive
[Come accedere a una macchina virtuale che esegue Windows Server][Logon]

[Come scollegare un disco dati da una macchina virtuale][Detach]

[Informazioni sugli account di archiviazione di Azure][Storage]

<!--Link references-->
[Attach]: storage-windows-attach-disk.md

[Disks]: ../dn790303.aspx

[VHD]: virtual-machines-create-upload-vhd-windows-server.md

[Logon]: virtual-machines-log-on-windows-server.md

[Detach]: storage-windows-detach-disk.md

[Storage]: ../storage-whatis-account.md


 

<!---HONumber=July15_HO1-->