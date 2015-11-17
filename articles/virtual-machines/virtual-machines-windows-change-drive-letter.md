<properties
	pageTitle="Trasformare l'unità D di una macchina virtuale in disco dati | Microsoft Azure"
	description="Descrive come modificare le lettere di unità per una macchina virtuale Windows creata usando il modello di distribuzione classico, in modo da poter usare l'unità D: come unità dati."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/03/2015"
	ms.author="cynthn"/>

# Usare l'unità D come unità dati in una macchina virtuale Windows 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.


Se si desidera usare l'unità D per archiviare i dati, seguire le istruzioni seguenti per usare una lettera di unità diversa per il disco temporaneo. Non utilizzare mai il disco temporaneo per archiviare i dati da conservare.

## Collegare il disco dati

Per prima cosa è necessario collegare il disco dati alla macchina virtuale. Per collegare un nuovo disco, vedere [Come collegare un disco dati a una macchina virtuale Windows][Attach].

Se si desidera usare un disco dati esistente, verificare di aver caricato anche il disco rigido virtuale nell'account di archiviazione. Per istruzioni, vedere i passaggi 3 e 4 in [Creare e caricare un disco rigido virtuale con Windows Server in Azure][VHD].


## Spostare temporaneamente pagefile.sys nell'unità C

1. Connettersi alla macchina virtuale. 

2. Fare clic con il pulsante destro del mouse sul menu **Start** e selezionare **Sistema**.

3. Nel menu a sinistra selezionare **Impostazioni di sistema avanzate**.

4. Nella sezione **Prestazioni** selezionare **Impostazioni**.

5. Selezionare la scheda **Avanzate**.

5. Nella sezione **Memoria virtuale** selezionare **Modifica**.

6. Selezionare l'unità **C** e quindi fare clic su **Dimensioni gestite dal sistema** e su **Imposta**.

7. Selezionare l'unità **D** e quindi fare clic su **Nessun file di paging** e su **Imposta**.

8. Fare clic su Applica. Verrà visualizzato un avviso che informa che è necessario riavviare il computer per rendere effettive le modifiche.

9. Riavviare la macchina virtuale.




## Modificare le lettere di unità 

1. Dopo il riavvio della macchina virtuale, accedere alla macchina virtuale.

2. Fare clic sul menu **Start**, digitare **diskmgmt.msc** e premere Invio. Verrà avviato Gestione disco.

3. Fare clic con il pulsante destro del mouse su **D**, l'unità di archiviazione temporanea, e quindi selezionare **Cambia lettera e percorso di unità**.

4. In Lettera unità selezionare l'unità **G** e quindi fare clic su **OK**.

5. Fare clic con il pulsante destro del mouse sul disco dati e selezionare **Cambia lettera e percorso di unità**.

6. In Lettera unità selezionare l'unità **D** e quindi fare clic su **OK**.

7. Fare clic con il pulsante destro del mouse su **G**, l'unità di archiviazione temporanea, e quindi selezionare **Cambia lettera e percorso di unità**.

8. In Lettera unità selezionare l'unità **E** e quindi fare clic su **OK**.

> [AZURE.NOTE]Se la macchina virtuale dispone di altri dischi o unità, usare lo stesso metodo per riassegnare le lettere di unità degli altri dischi e unità. Si desidera la configurazione seguente per il disco: - C: disco del sistema operativo - D: disco dati - E: disco temporaneo



## Spostare nuovamente pagefile.sys nell'unità di archiviazione temporanea 

1. Fare clic con il pulsante destro del mouse sul menu **Start** e selezionare **Sistema**.

2. Nel menu a sinistra selezionare **Impostazioni di sistema avanzate**.

3. Nella sezione **Prestazioni** selezionare **Impostazioni**.

4. Selezionare la scheda **Avanzate**.

5. Nella sezione **Memoria virtuale** selezionare **Modifica**.

6. Selezionare l'unità del sistema operativo **C** e quindi fare clic su **Nessun file di paging** e su **Imposta**.

7. Selezionare l'unità di archiviazione temporanea **E** e quindi fare clic su **Dimensioni gestite dal sistema** e su **Imposta**.

8. Fare clic su **Apply**. Verrà visualizzato un avviso che informa che è necessario riavviare il computer per rendere effettive le modifiche.

9. Riavviare la macchina virtuale.




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

<!---HONumber=Nov15_HO2-->