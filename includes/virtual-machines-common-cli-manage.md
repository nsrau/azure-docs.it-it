L'interfaccia della riga di comando di Azure 2.0 consente di creare e gestire le risorse di Azure in macOS, Linux e Windows. Questo articolo illustra in modo dettagliato i comandi più comuni per la creazione e la gestione delle macchine virtuali.

Questo articolo richiede l'interfaccia della riga di comando di Azure 2.0.4 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). È anche possibile usare [Cloud Shell](/azure/cloud-shell/quickstart) dal browser.

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Comandi di base di Azure Resource Manager nell'interfaccia della riga di comando di Azure
Per una guida più dettagliata con parametri e opzioni della riga di comando specifici, è possibile usare la Guida in linea dei comandi e le relative opzioni digitando `az <command> <subcommand> --help`.

### <a name="create-vms"></a>Creare VM
| Attività | Comandi dell'interfaccia della riga di comando di Azure |
| --- | --- |
| Creare un gruppo di risorse | `az group create --name myResourceGroup --location eastus` |
| Creare una macchina virtuale Linux | `az vm create --resource-group myResourceGroup --name myVM --image ubuntults` |
| Creare un'app Windows | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### <a name="manage-vm-state"></a>Gestire lo stato delle VM
| Attività | Comandi dell'interfaccia della riga di comando di Azure |
| --- | --- |
| Avviare una macchina virtuale | `az vm start --resource-group myResourceGroup --name myVM` |
| Arrestare una macchina virtuale | `az vm stop --resource-group myResourceGroup --name myVM` |
| Deallocare una macchina virtuale | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| Riavviare una macchina virtuale | `az vm restart --resource-group myResourceGroup --name myVM` |
| Ridistribuire una VM | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| Eliminare una macchina virtuale | `az vm delete --resource-group myResourceGroup --name myVM` |

### <a name="get-vm-info"></a>Ottenere informazioni sulle VM
| Attività | Comandi dell'interfaccia della riga di comando di Azure |
| --- | --- |
| Elenco delle macchine virtuali | `az vm list` |
| Visualizzare informazioni su una macchina virtuale | `az vm show --resource-group myResourceGroup --name myVM` |
| Ottenere l'utilizzo delle risorse della macchina virtuale | `az vm list-usage --location eastus` |
| Ottenere tutte le dimensioni delle macchine virtuali disponibili | `az vm list-sizes --location eastus` |

## <a name="disks-and-images"></a>Dischi e immagini
| Attività | Comandi dell'interfaccia della riga di comando di Azure |
| --- | --- |
| Aggiungere un disco dati a una macchina virtuale | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new ` |
| Rimuovere un disco dati da una macchina virtuale | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| Ridimensionare un disco | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| Snapshot di un disco | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| Creare un'immagine di una VM | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| Creare una VM in base a un'immagine | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>Passaggi successivi
Per esempi aggiuntivi dei comandi dell'interfaccia della riga di comando, vedere l'esercitazione [Creare e gestire VM Linux con l'interfaccia della riga di comando di Azure](../articles/virtual-machines/linux/tutorial-manage-vm.md).

