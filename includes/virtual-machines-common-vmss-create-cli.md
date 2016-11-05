I set di scalabilità di macchine virtuali consentono di gestire più VM come un unico set. A livello generale, i set di scalabilità presentano i vantaggi e gli svantaggi seguenti:

Vantaggi:

1. Disponibilità elevata. Ogni set di scalabilità inserisce le relative VM in un set di disponibilità con 5 domini di errore (FD) e 5 domini di aggiornamento (UD) per garantire la disponibilità (per altre informazioni su domini di errore e domini di aggiornamento, vedere [Gestire la disponibilità delle macchine virtuali Linux](../articles/virtual-machines/virtual-machines-linux-manage-availability.md)).
2. Facile integrazione con il servizio di bilanciamento del carico di Azure e il gateway app.
3. Facile integrazione con la scalabilità automatica di Azure.
4. Distribuzione, gestione e pulizia semplificate delle VM.
5. Supporto delle versioni comuni di Windows e Linux e di immagini personalizzate.

Svantaggi:

1. Impossibile collegare dischi dati a istanze di VM in un set di scalabilità. Di contro, è necessario utilizzare l'archiviazione BLOB, file di Azure, tabelle di Azure o un'altra soluzione di archiviazione.

## Creazione rapida tramite riga di comando di Azure
Ove non già fatto in precedenza, è possibile ottenere una [versione di valutazione gratuita della sottoscrizione di Azure](https://azure.microsoft.com/pricing/free-trial/) e la [riga di comando di Azure](../articles/xplat-cli-install.md) [connessa all'account Azure](../articles/xplat-cli-connect.md). Al termine dell'operazione, è possibile eseguire i comandi seguenti per la creazione rapida di un set di scalabilità di VM:

```bash
# make sure we are in resource manager mode 
azure config mode arm

# quick-create a VM scale set
#
# generic syntax:
# azure vmss quick-create -n VMSS-NAME -g RESOURCE-GROUP-NAME -l LOCATION -u USERNAME -p PASSWORD -C INSTANCE-COUNT -Q IMAGE-URN
#
# example:
azure vmss quick-create -n negatvmss -g negatvmssrg -l westus -u negat -p P4$$w0rd -C 5 -Q Canonical:UbuntuServer:14.04.4-LTS:latest
```

Se si desidera personalizzare il percorso o l'URN immagine, esaminare i comandi `azure location list` e `azure vm image {list-publishers|list-offers|list-skus|list|show}`.

Il set di scalabilità viene creato dopo la restituzione di questo comando. Questo set di scalabilità avrà un servizio di bilanciamento del carico con regole NAT che eseguono il mapping della porta 50000+i sulla porta 22 della VM i. Dopo aver determinato l'FQDN del servizio di bilanciamento del carico, sarà quindi possibile usare SSH per accedere alle VM:

```bash
# list load balancers in the resource group we created
#
# generic syntax:
# azure network lb list -g RESOURCE-GROUP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb list -g nsgvmsrg | grep nsgvmssrg)
split_line=( $line )
lb_name=${split_line[1]}

# now that we have the name of the load balancer, we can show the details to find which Public IP (PIP) is associated with it
#
# generic syntax:
# azure network lb show -g RESOURCE-GROUP-NAME -n LB-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb show -g nsgvmssrg -n $lb_name | grep loadBalancerFrontEnd)
split_line=( $line )
pip_name=${split_line[4]}

# now that we have the name of the public IP address, we can show the details to find the FQDN
#
# generic syntax:
# azure network public-ip show -g RESOURCE-GROUP-NAME -n PIP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network public-ip show -g nsgvmssrg -n $pip_name | grep FQDN)
split_line=( $line )
FQDN=${split_line[3]}

# now that we have the FQDN, we can ssh on port 50,000+i to ssh into VM i (where i is 0-indexed)
#
# example to ssh into VM "0":
ssh -p 50000 $FQDN
```

## Passaggi successivi
Per informazioni generali vedere la [pagina di destinazione principale per i set di scalabilità di VM](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

Per la documentazione vedere la [pagina della documentazione principale per i set di scalabilità di VM](https://azure.microsoft.com/documentation/services/virtual-machines-scale-sets/).

Ad esempio, per i modelli di Azure Resource Manager con set di scalabilità di VM, cercare "vmss" nel [repository di GitHub di modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates).

<!---HONumber=AcomDC_0406_2016-->