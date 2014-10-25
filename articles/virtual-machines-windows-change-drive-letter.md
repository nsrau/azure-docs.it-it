<properties title="How To Change the Drive Letter of the Windows Temporary Disk" pageTitle="How To Change the Drive Letter of the Windows Temporary Disk" description="Describes how to remap the temporary disk on a Windows VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Come modificare la lettera di unità del disco temporaneo di Windows

È possibile modificare la lettera di unità del disco temporaneo se è necessario usare l'unità D per uno scopo diverso. È molto probabile che questa operazione venga eseguita per supportare un'applicazione o un servizio che usa l'unità D come posizione di archiviazione permanente.

Prima di iniziare, verificare di avere quanto segue:

-   Un disco dati collegato da usare per l'archiviazione del file di paging di Windows (pagefile.sys) durante la procedura. Per le istruzioni, vedere [Come collegare un disco dati a una macchina virtuale Windows][Come collegare un disco dati a una macchina virtuale Windows].
-   Un disco rigido virtuale caricato nell'account di archiviazione, se si vuole usare un disco rigido virtuale di un disco dati esistente nell'unità D. Per le istruzioni, vedere i passaggi 3 e 4 in [Creare e caricare un disco rigido virtuale di Windows Server in Azure][Creare e caricare un disco rigido virtuale di Windows Server in Azure].

## Modificare la lettera di unità

1.  Accedere alla macchina virtuale.

2.  Spostare pagefile.sys dall'unità D a un'altra unità.

3.  Riavviare la macchina virtuale.

4.  Eseguire di nuovo l'accesso e modificare la lettera di unità D in E.

5.  Dal [portale di gestione di Azure][portale di gestione di Azure] collegare un disco dati esistente o un disco dati vuoto.

6.  Accedere di nuovo alla macchina virtuale, inizializzare il disco e assegnare la lettera di unità D per il disco appena collegato.

7.  Verificare che l'unità E sia stata mappata al disco di archiviazione temporanea.

8.  Spostare pagefile.sys dall'altra unità all'unità E.

## Risorse aggiuntive

[Come accedere a una macchina virtuale che esegue Windows Server][Come accedere a una macchina virtuale che esegue Windows Server]

[Come scollegare un disco dati da una macchina virtuale][Come scollegare un disco dati da una macchina virtuale]

[Informazioni sull'account di archiviazione][Informazioni sull'account di archiviazione]

<!--Link references-->

  [Come collegare un disco dati a una macchina virtuale Windows]: ../storage-windows-attach-disk
  [Creare e caricare un disco rigido virtuale di Windows Server in Azure]: ../virtual-machines-create-upload-vhd-windows-server/
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [Come accedere a una macchina virtuale che esegue Windows Server]: ../virtual-machines-log-on-windows-server/
  [Come scollegare un disco dati da una macchina virtuale]: ../storage-windows-detach-disk/
  [Informazioni sull'account di archiviazione]: ../storage-whatis-account/
