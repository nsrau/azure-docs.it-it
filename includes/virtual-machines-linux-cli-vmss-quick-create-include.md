Se non è già stato fatto, è possibile ottenere [una versione di valutazione gratuita di una sottoscrizione di Azure](https://azure.microsoft.com/pricing/free-trial/) e l'[interfaccia della riga di comando di Azure](../articles/xplat-cli-install.md) [connessa all'account Azure](../articles/xplat-cli-connect.md). Verificare che l'interfaccia della riga di comando di Azure sia in modalità Resource Manager come indicato di seguito:

```azurecli
azure config mode arm
```

A questo punto creare i set di scalabilità usando il comando `azure vmss quick-create`. Nell'esempio seguente viene creato un set di scalabilità di Linux denominato `myVMSS` con 5 istanze di macchine virtuali nel gruppo di risorse denominato `myResourceGroup`:

```azurecli
azure vmss quick-create -n myVMSS -g myResourceGroup -l westus \
    -u ops -p P@ssw0rd! \
    -C 5 -Q Canonical:UbuntuServer:16.04.0-LTS:latest
```

Nell'esempio seguente viene creato un set di scalabilità di Windows con la stessa configurazione:

```azurecli
azure vmss quick-create -n myVMSS -g myResourceGroup -l westus \
    -u ops -p P@ssw0rd! \
    -C 5 -Q MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest
```

Se si vuole personalizzare la località o l'URN dell'immagine, esaminare i comandi `azure location list` e `azure vm image {list-publishers|list-offers|list-skus|list|show}`.

Il set di scalabilità viene creato dopo la restituzione di questo comando. Questo set di scalabilità avrà un servizio di bilanciamento del carico con regole NAT che eseguono il mapping della porta 50000+i sulla porta 22 della VM i. Dopo aver determinato l'FQDN del servizio di bilanciamento del carico, sarà possibile usare SSH per la connessione alle macchine virtuali:

```bash
# (if you decide to run this as a script, please invoke using bash)

# list load balancers in the resource group we created
#
# generic syntax:
# azure network lb list -g RESOURCE-GROUP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb list -g negatvmssrg | grep negatvmssrg)
split_line=( $line )
lb_name=${split_line[1]}

# now that we have the name of the load balancer, we can show the details to find which Public IP (PIP) is 
# associated to it
#
# generic syntax:
# azure network lb show -g RESOURCE-GROUP-NAME -n LOAD-BALANCER-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb show -g negatvmssrg -n $lb_name | grep loadBalancerFrontEnd)
split_line=( $line )
pip_name=${split_line[4]}

# now that we have the name of the public IP address, we can show the details to find the FQDN
#
# generic syntax:
# azure network public-ip show -g RESOURCE-GROUP-NAME -n PIP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network public-ip show -g negatvmssrg -n $pip_name | grep FQDN)
split_line=( $line )
FQDN=${split_line[3]}

# now that we have the FQDN, we can use ssh on port 50,000+i to connect to VM i (where i is 0-indexed)
#
# example to connct via ssh into VM "0":
ssh -p 50000 negat@$FQDN
```

<!--HONumber=Feb17_HO2-->


