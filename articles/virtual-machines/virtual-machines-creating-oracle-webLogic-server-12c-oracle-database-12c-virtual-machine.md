<properties title="Creating an Oracle WebLogic Server 12c and Oracle Database 12c virtual machine in Azure" pageTitle="Creazione di una macchina virtuale Oracle WebLogic Server 12c e Oracle Database 12c in Azure" description="Seguire un esempio di creazione di un’immagine Oracle WebLogic Server 12c e Oracle Database 12c in esecuzione su Windows Server 2012 in Microsoft Azure." services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />

#Creare una macchina virtuale Oracle WebLogic Server 12c e Oracle Database 12c in Azure

Nell'esempio seguente viene mostrato come creare una macchina virtuale basata su un'immagine Oracle WebLogic Server 12c e Oracle Database 12c fornita da Microsoft in esecuzione su Windows Server 2012 in Azure.

##Per creare una macchina virtuale Oracle WebLogic Server 12c e Oracle Database 12c in Azure

1. Accedere al [portale di Azure](https://ms.portal.azure.com/).

2.	Fare clic su **Marketplace**, su **Calcolo**, quindi digitare **Oracle** nella casella di ricerca.

3.	Selezionare l’immagine **Oracle Database 12c e WebLogic Server 12c Standard Edition su Windows Server 2012** o **Oracle Database 12c e WebLogic Server 12c Enterprise Edition su Windows Server 2012**. Esaminare le informazioni su questa immagine (come le dimensioni minime consigliate), quindi fare clic su **Avanti**.

4.	Specificare un **Nome host** per la macchina virtuale.

5.	Specificare un **Nome utente** per la macchina virtuale. Si noti che il nome utente serve per l'accesso in modalità remota alla macchina virtuale; non è il nome utente del database Oracle.

6.	Specificare e confermare una password per la macchina virtuale o fornire una chiave pubblica Secure Shell (SSH).

7.	Scegliere un **livello di prezzo**. Si noti che i livelli di prezzo consigliati vengono visualizzati per impostazione predefinita. Per visualizzare tutte le opzioni di configurazione, fare clic su**Visualizza tutto**in alto a destra.

8. Impostare le configurazioni facoltative in base alle esigenze (vedere[Informazioni sulle impostazioni di configurazione di macchina virtuale Azure](https://msdn.microsoft.com/library/azure/dn763935.aspx). Seguire queste considerazioni:

	a. Lasciare **Account di archiviazione** invariato per creare un nuovo account di archiviazione con il nome della macchina virtuale.

	b. Lasciare **Set di disponibilità** come **Non configurato**.

	c. Al momento non aggiungere alcun endpoint.

9.	Scegliere o creare un gruppo di risorse. Per ulteriori informazioni, vedere[Utilizzare il portale di anteprima di Azure per gestire le risorse di Azure](resource-group-portal.md).

10. Scegliere una **Sottoscrizione**.

11. Scegliere una **Posizione**.


##Per creare il database ospitato in questa macchina virtuale

Seguire le istruzioni in [Creazione di una macchina virtuale Oracle Database 12c in Azure](virtual-machines-creating-oracle-database-virtual-machine.md), iniziando con la sezione **Creazione del database utilizzando la macchina virtuale Oracle Database 12c in Azure**.

##Per configurare il database Oracle WebLogic Server 12c ospitato in questa macchina virtuale
Seguire le istruzioni in [Creazione di una macchina virtuale Oracle WebLogic Server 12c in Azure](virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine.md), iniziando con la sezione **Configurazione della macchina virtuale Oracle WebLogic Server 12c in Azure**. Se si desidera configurare un cluster WebLogic Server, vedere anche [Creazione di un cluster Oracle WebLogic Server 12c in Azure](virtual-machines-creating-oracle-webLogic-server-12c-cluster.md).

##Risorse aggiuntive
[Considerazioni varie sulle immagini di macchine virtuali Oracle](miscellaneous-considerations-for-oracle-virtual-machine-images-new-article.md)

[Elenco delle immagini di macchine virtuali Oracle](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

[Connessione a Oracle Database da un'applicazione Java](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

[Oracle WebLogic Server 12c con Linux su Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

[Oracle Database 2 Day DBA 12c Release 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=August15_HO6-->