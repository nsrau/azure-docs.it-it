<properties
	pageTitle="Accedere a una VM di Windows | Microsoft Azure"
	description="Usare il portale di Azure per accedere a una macchina virtuale di Windows creata con il modello di distribuzione di Gestione risorse."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="cynthn"/>


# Accedere a una macchina virtuale di Windows tramite il portale di Azure


Nel portale di Azure, si utilizza il pulsante **Connetti** per avviare una sessione di Desktop remoto e accedere a una macchina virtuale Windows.


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-classic-connect-logon.md).


## Connettersi alla macchina virtuale

1. Accedere al portale di Azure.

2. Fare clic su **Macchine virtuali**, quindi selezionare la macchina virtuale.

3. Nella barra dei comandi in fondo alla pagina, fare clic su **Connetti**.

	![Accesso alla macchina virtuale](./media/arm_log_on_windows_vm/rm_windows_connect.png)
	

4. Facendo clic su **Connetti** si crea e si scarica un file Remote Desktop Protocol (file con estensione rdp). Fare clic su **Apri** per utilizzare questo file.

	![Accesso alla macchina virtuale](./media/arm_log_on_windows_vm/rm_connect_rdp_file.png)
	
5. Nella finestra di Desktop remoto, fare clic su **Connetti** per continuare.

	![Procedere alla connessione](./media/arm_log_on_windows_vm/rm_connect_unknown.png)

	
6. Nella finestra Protezione di Windows, digitare le credenziali dell'account della macchina virtuale, quindi fare clic su **OK**.

 	Nella maggior parte dei casi, le credenziali corrispondono al nome utente e alla password dell'account locale specificati al momento della creazione della macchina virtuale. Il dominio è il nome della macchina virtuale immesso come *nome VM*& #92;*nome utente*.
	
	Se la macchina virtuale appartiene a un dominio nell'organizzazione, assicurarsi che il nome utente contenga il nome del dominio nel formato *Dominio*&#92;*Nome utente*. L'account deve appartenere al gruppo degli amministratori o deve possedere privilegi di accesso remoto alla VM.
	
	Se la macchina virtuale è un controller di dominio, digitare nome utente e password di un account amministratore di dominio per tale dominio.

7.	Fare clic su **Sì** per verificare l'identità della macchina virtuale e terminare la procedura di accesso.

	![Verificare l'identità della macchina virtuale](./media/arm_log_on_windows_vm/rm_connect_cert.png)

## Passaggi successivi

In caso di problemi quando si cerca di eseguire la connessione, vedere [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).

<!---HONumber=AcomDC_0330_2016-->