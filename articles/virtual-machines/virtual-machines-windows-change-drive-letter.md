<properties
	pageTitle="Modificare la lettera di unità del disco temporaneo | Microsoft Azure"
	description="Modificare la lettera di unità del disco temporaneo su una macchina virtuale creata con un modello di distribuzione classica."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn
"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/27/2015"
	ms.author="cynthn"/>

#Modificare la lettera di unità del disco temporaneo di Windows su una macchina virtuale creata con un modello di distribuzione classica

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]In questo articolo viene illustrata la creazione delle risorse con il modello di distribuzione classica.

Se si desidera usare l'unità D per archiviare i dati, seguire le istruzioni seguenti per usare un'unità diversa per il disco temporaneo. Non utilizzare mai il disco temporaneo per archiviare i dati da conservare.

Prima di iniziare è necessario disporre di un disco dati collegato alla macchina virtuale da usare per l'archiviazione del file di paging di Windows (pagefile.sys) durante la procedura. Se non si dispone di un disco dati collegato, vedere [Come collegare un disco dati a una macchina virtuale Windows][Attach]. Per istruzioni su come individuare quali dischi sono collegati, vedere "Individuazione del disco" in [Come scollegare un disco dati da una macchina virtuale Windows][Detach].

Se si desidera utilizzare un disco dati esistente nell'unità D, assicurarsi di aver caricato anche il disco rigido virtuale nell'account di archiviazione. Per istruzioni, vedere i passaggi 3 e 4 in [Creazione e caricamento di un disco rigido virtuale di Windows Server in Azure][VHD].

> [AZURE.WARNING]Se si ridimensiona o si "arresta (dealloca)" una macchina virtuale, potrebbe essere attivata una un posizionamento della macchina virtuale su un nuovo hypervisor. Tale posizionamento può attivare un evento di manutenzione pianificato o non pianificato. In questo scenario il disco temporaneo sarà riassegnato alla prima lettera di unità disponibile. Se si dispone di un'applicazione che richiede specificamente l'unità "D", verificare che dopo lo spostamento del file di paging, si assegna un nuovo disco permanente, attribuendogli la lettera D. Azure non prenderà nuovamente la lettera D.

> [AZURE.WARNING]Se si ridimensiona una macchina virtuale dopo lo spostamento esplicito del file di paging, notare che è possibile riscontrare un errore all'avvio se il disco temporaneo della nuova macchina virtuale non è sufficientemente grande da contenere il file di paging delle dimensioni originali della VM. Questo errore può verificarsi anche se l'unità temporanea non è stata impostata alla successiva lettera di unità disponibile, forzando Windows a fare riferimento a una lettera di unità non valida nella configurazione del file di paging mentre Azure crea l’unità temporanea con la lettera di unità disponibile successiva.

##Modificare la lettera di unità

1. Accedere alla macchina virtuale. Per informazioni dettagliate, vedere [Come accedere a una macchina virtuale che esegue Windows Server][Logon].

2. Spostare pagefile.sys dall'unità D a un'altra unità.

3. Riavviare la macchina virtuale.

4. Eseguire di nuovo l'accesso e modificare la lettera di unità D in E.

5. Dal [portale di Azure](http://manage.windowsazure.com), collegare un disco dati esistente o un disco dati vuoto.

6.	Accedere di nuovo alla macchina virtuale, inizializzare il disco e assegnare la lettera D all'unità per il disco appena collegato.

7.	Verificare che l'unità E sia stata mappata al disco temporaneo.

8.	Spostare pagefile.sys dall'altra unità all'unità E.

9.	Riavviare la macchina virtuale.



## Risorse aggiuntive
[Come accedere a una macchina virtuale che esegue Windows Server][Logon]

[Come scollegare un disco dati da una macchina virtuale di Windows][Detach]

[Informazioni sugli account di archiviazione di Azure][Storage]

<!--Link references-->
[Attach]: storage-windows-attach-disk.md



[VHD]: virtual-machines-create-upload-vhd-windows-server.md

[Logon]: virtual-machines-log-on-windows-server.md

[Detach]: storage-windows-detach-disk.md

[Storage]: ../storage-whatis-account.md

<!---HONumber=Sept15_HO4-->