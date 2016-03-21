
Se si sono riscontrate difficoltà nella risoluzione dei problemi relativi alla connessione Desktop remoto (RDP) alla macchina virtuale Azure basata su Windows o nella risoluzione dei problemi relativi alla connessione SSH alla macchina virtuale Azure basata su Linux, questo articolo fornirà supporto per una risoluzione autonoma di tali anomalie, senza ricorrere al supporto o al ridimensionamento della macchina virtuale. Microsoft Azure ridistribuirà la macchina virtuale al richiamo dell'operazione di ridistribuzione attraverso Azure PowerShell.

Si noti che dopo il completamento di questa operazione, i dati temporanei del disco andranno persi e gli indirizzi IP dinamici associati alla macchina virtuale saranno aggiornati.


## Uso di Azure PowerShell

Assicurarsi che sia installata la versione più recente di Azure PowerShell 1.x. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](../articles/powershell-install-configure.md).

Utilizzare questo comando di Azure PowerShell per ridistribuire la macchina virtuale:

	Set-AzureRmVM -Redeploy -ResourceGroupName $rgname -Name $vmname 


Durante l'esecuzione di questo comando controllare la macchina virtuale nel [portale di Azure](https://portal.azure.com). Si noti che lo **stato** della VM viene modificato come segue:

1. Lo **stato** iniziale è *in esecuzione*

	![Redeploy initial status](./media/virtual-machines-common-redeploy-to-new-node/statusrunning1.png)

2. Lo **stato** passa a indicare *l'aggiornamento*

	![Redeploy status Updating](./media/virtual-machines-common-redeploy-to-new-node/statusupdating.png)

3. Lo **stato** passa a indicare *l'avvio*

	![Redeploy status Starting](./media/virtual-machines-common-redeploy-to-new-node/statusstarting.png)

4. Lo **stato** torna a indicare *in esecuzione*

	![Redeploy final status](./media/virtual-machines-common-redeploy-to-new-node/statusrunning2.png)

Quando lo **stato** indica nuovamente in *esecuzione*, la VM è stata ridistribuita correttamente.

<!---HONumber=AcomDC_0309_2016-->