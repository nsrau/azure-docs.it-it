<properties
	pageTitle="Oracle WebLogic Server e Database VM | Microsoft Azure"
	description="Creare un’immagine Oracle WebLogic Server c 12 e Database Oracle 12c Azure in esecuzione su Windows Server 2012, utilizzando il modello di distribuzione di gestione delle risorse."
	services="virtual-machines-windows"
	authors="bbenz"
	documentationCenter=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="06/22/2015"
	ms.author="bbenz" />

#Creare una macchina virtuale Oracle WebLogic Server 12c e Oracle Database 12c in Azure

Nell'esempio seguente viene mostrato come creare una macchina virtuale basata su un'immagine Oracle WebLogic Server 12c e Oracle Database 12c fornita da Microsoft in esecuzione su Windows Server 2012 in Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.


##Per creare una macchina virtuale Oracle WebLogic Server 12c e Oracle Database 12c in Azure

1. Accedere al [portale di Azure](https://ms.portal.azure.com/).

2.	Fare clic su **Marketplace**, su **Calcolo**, quindi digitare **Oracle** nella casella di ricerca.

3.	Selezionare l’immagine **Oracle Database 12c e WebLogic Server 12c Standard Edition su Windows Server 2012** o **Oracle Database 12c e WebLogic Server 12c Enterprise Edition su Windows Server 2012**. Esaminare le informazioni su questa immagine (come le dimensioni minime consigliate), quindi fare clic su **Avanti**.

4.	Specificare un **Nome host** per la macchina virtuale.

5.	Specificare un **Nome utente** per la macchina virtuale. Si noti che il nome utente serve per l'accesso in modalità remota alla macchina virtuale; non è il nome utente del database Oracle.

6.	Specificare e confermare una password per la macchina virtuale o fornire una chiave pubblica Secure Shell (SSH).

7.	Scegliere un **piano tariffario**. Si noti che i livelli di prezzo consigliati vengono visualizzati per impostazione predefinita. Per visualizzare tutte le opzioni di configurazione, fare clic su**Visualizza tutto**in alto a destra.

8. Impostare le configurazioni facoltative in base alle esigenze. Seguire queste considerazioni:

	a. Lasciare **Account di archiviazione** invariato per creare un nuovo account di archiviazione con il nome della macchina virtuale.

	b. Lasciare **Set di disponibilità** come **Non configurato**.

	c. Al momento non aggiungere alcun endpoint.

9.	Scegliere o creare un gruppo di risorse. Per ulteriori informazioni, vedere [Utilizzare il portale di Azure per gestire le risorse di Azure](../azure-portal/resource-group-portal.md).

10. Scegliere una **Sottoscrizione**.

11. Scegliere una **Posizione**.


##Per creare il database ospitato in questa macchina virtuale

Seguire le istruzioni in [Creazione di una macchina virtuale Oracle Database 12c in Azure](virtual-machines-windows-classic-create-oracle-database.md), iniziando con la sezione **Creazione del database utilizzando la macchina virtuale Oracle Database 12c in Azure**.

##Per configurare il database Oracle WebLogic Server 12c ospitato in questa macchina virtuale
Seguire le istruzioni in [Creazione di una macchina virtuale Oracle WebLogic Server 12c in Azure](virtual-machines-windows-create-oracle-weblogic-server-12c.md), iniziando con la sezione **Configurazione della macchina virtuale Oracle WebLogic Server 12c in Azure**.

##Risorse aggiuntive
[Considerazioni varie sulle immagini di macchine virtuali Oracle](virtual-machines-windows-classic-oracle-considerations.md)

[Elenco delle immagini di macchine virtuali Oracle](virtual-machines-linux-classic-oracle-images.md)

[Connessione a Oracle Database da un'applicazione Java](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

[Oracle WebLogic Server 12c con Linux su Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

[Oracle Database 2 Day DBA 12c Release 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=AcomDC_0413_2016-->