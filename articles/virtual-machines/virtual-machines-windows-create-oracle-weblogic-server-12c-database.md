<properties
	pageTitle="Oracle WebLogic Server e Database VM | Microsoft Azure"
	description="Creare un’immagine Oracle WebLogic Server c 12 e Database Oracle 12c Azure in esecuzione su Windows Server 2012, utilizzando il modello di distribuzione di gestione delle risorse."
	services="virtual-machines-windows"
	authors="rickstercdn"
	manager="timlt"
	documentationCenter=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="05/17/2016"
	ms.author="rickstercdn" />

#Creare una macchina virtuale Oracle WebLogic Server 12c e Oracle Database 12c in Azure

[AZURE.INCLUDE [virtual-machines-common-oracle-support](../../includes/virtual-machines-common-oracle-support.md)]

Questo articolo illustra come creare un database Oracle WebLogic Server 12c e Oracle Database 12c in un'immagine creata in precedenza con il software Oracle installato su Windows Server 2012 in Azure.

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

<!---HONumber=AcomDC_0601_2016-->