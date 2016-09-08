<properties
	pageTitle="Clonare una macchina virtuale classica in Azure Resource Manager usando script PowerShell"
	description="Questo articolo illustra come clonare una macchina virtuale classica in Azure Resource Manager usando script PowerShell"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="singhkays"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/03/2016"
	ms.author="singhkay"/>

# Clonare una macchina virtuale classica in Azure Resource Manager usando script PowerShell

Questo articolo illustra come usare gli script disponibili in [Azure/classic-iaas-resourcemanager-migration](https://github.com/Azure/classic-iaas-resourcemanager-migration) per clonare una **singola** macchina virtuale classica nel modello di distribuzione Azure Resource Manager.

>[AZURE.IMPORTANT]La clonazione con questi script comporta tempi di inattività della macchina virtuale classica. Se si è interessati a una modalità di migrazione supportata dalla piattaforma, vedere
- [Migrazione supportata dalla piattaforma di risorse IaaS dal modello classico al modello di Azure Resource Manager](./virtual-machines-windows-migration-classic-resource-manager.md)
- [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello classico al modello di Azure Resource Manager](./virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Eseguire la migrazione di risorse IaaS dal modello classico al modello di Azure Resource Manager tramite Azure PowerShell](./virtual-machines-windows-ps-migration-classic-resource-manager.md)

## Come ottenere gli script

>[AZURE.NOTE]Gli script PowerShell non sono supportati ufficialmente dal Supporto tecnico Microsoft. Sono quindi open source in Github e perciò sono graditi PR con suggerimenti per correzioni o altri scenari.

È possibile ottenere gli script scaricando il file zip dal repository specificato sopra o clonare il repository con **uno** dei due comandi seguenti

```
git clone https://github.com/Azure/classic-iaas-resourcemanager-migration.git
```

**OPPURE**

```
git clone git@github.com:Azure/classic-iaas-resourcemanager-migration.git
```

## Importare il modulo di migrazione

Il passaggio successivo consiste nell'importare il modulo nella sessione di PowerShell. È possibile eseguire questa operazione con il comando seguente

```
Import-Module .\asm2arm.psd1
```

## Autenticarsi con le sottoscrizioni per il modello di distribuzione classica e Azure Resource Manager

Prima di poter clonare la macchina virtuale classica, è necessario autenticare la sessione di PowerShell per gli stack del modello di distribuzione classica e di Azure Resource Manager. A questo scopo, è possibile usare i cmdlet seguenti

```
Add-AzureAccount
Login-AzureRmAccount
```

>[AZURE.IMPORTANT]Assicurarsi che le sottoscrizioni predefinite siano selezionate con `Select-AzureSubscription` per il modello di distribuzione classica e con Set-AzureRmContext per Azure Resource Manager

## Uso degli script

### Cmdlet

L'importazione del modulo aggiungerà i due cmdlet seguenti alla sessione.

```
Add-AzureSMVmToRM
New-AzureSmToRMDeployment
```

Questi cmdlet svolgono la funzionalità specificata di seguito.

#### Add-AzureSMVmToRM
- Genera un set di modelli di Azure Resource Manager e script PowerShell imperativi, per copiare i BLOB del disco e se la VM ha estensioni dell'agente di macchine virtuali, data una macchina virtuale specifica.

>[AZURE.IMPORTANT]Se si specifica l'opzione `-Deploy`, questo cmdlet chiama il cmdlet `New-AzureSmToRMDeployment` per distribuire i modelli di Azure Resource Manager e gli script PowerShell imperativi generati, indicati sopra.

#### New-AzureSmToRMDeployment
- Distribuisce i modelli e gli script PowerShell imperativi generati dal cmdlet `Add-AzureSMVmToRM` per avviare la migrazione.

### Identificare la macchina virtuale classica da clonare

Questa operazione può essere eseguita archiviando lo stato della macchina virtuale classica in una variabile e passandola al cmdlet `Add-AzureSMVmToRM` o direttamente con i parametri `ServiceName` e `Name` della VM. Dopo aver identificato la macchina virtuale classica, è possibile usare il cmdlet `Add-AzureSMVmToRM` per clonarla.

```
$vm = Get-AzureVm -ServiceName acloudservice -Name atestvm
Add-AzureSMVmToRM -VM $vm -ResourceGroupName aresourcegroupname -DiskAction CopyDisks -OutputFileFolder D:\myarmtemplates -AppendTimeStampForFiles -Deploy
```

**OPPURE**

```
Add-AzureSMVmToRM -ServiceName acloudservice -Name atestvm -ResourceGroupName aresourcegroupname -DiskAction CopyDisks -OutputFileFolder D:\myarmtemplates -AppendTimeStampForFiles -Deploy
```

>[AZURE.IMPORTANT]Poiché negli esempi precedenti si usa l'opzione `-Deploy`, non è necessario usare il cmdlet `New-AzureSmToRMDeployment`.

## Come funziona la migrazione con questi script

I cmdlet eseguono una serie di passaggi per la clonazione di classVM e generano risorse come tabelle hash di PowerShell personalizzate per i provider di risorse di archiviazione, rete e calcolo. Le tabelle hash che rappresentano le risorse vengono aggiunte a una matrice, che in seguito viene trasformata in un modello, mediante la serializzazione in JSON, e scritte in un file.

Il modello crea i file in base all'esistenza di estensioni dell'agente di macchine virtuali classiche e il valore dell'opzione DiskAction. Tutti i file vengono inseriti nella directory specificata dal parametro OutputFileFolder. I file sono:

1. `<ServiceName>-<VMName>-setup<optional timestamp>.json`: questo file rappresenta le risorse che è necessario preparare prima che la macchina virtuale classica venga clonata ed è potenzialmente lo stesso per tutte le macchine virtuali successive. Non viene mantenuto lo stato tra le esecuzioni successive, ma poiché è necessario effettuare il provisioning di un account di archiviazione prima che si verifichi un'operazione di copia di BLOB, che viene eseguita in modo imperativo, è logico raggruppare risorse analoghe in uno solo.

2.  `<ServiceName>-<VMName>-deploy<optional timestamp>.json`: contiene il modello per la macchina virtuale di Resource Manager.
3.  `<ServiceName>-<VMName>-parameters<optional timestamp>.json`: contiene i parametri effettivi passati ai modelli.
4.  `<ServiceName>-<VMName>-setextensions<optional timestamp>.json`: set di cmdlet PowerShell da eseguire per l'impostazione di estensioni dell'agente di macchine virtuali.
4.  `<ServiceName>-<VMName>-copydisks<optional timestamp>.json`: set di cmdlet PowerShell da eseguire per copiare i BLOB del disco, se è specificata l'opzione CopyDisks.

Se è impostato il flag-Deploy, dopo la generazione dei file il cmdlet distribuisce il modello <ServiceName>-<VMName>-setup.json, copia i BLOB del disco della macchina virtuale di origine se il parametro DiskAction è impostato su CopyDisks e quindi distribuisce il modello <ServiceName>-<VMName>-deploy.json usando il file <ServiceName>-<VMName>-parameters.json per i parametri. Dopo avere completato la distribuzione della macchina virtuale, se è presente uno script imperativo per le estensioni dell'agente di macchine virtuali o uno script per la copia dei dischi, i file vengono eseguiti.

### Dettagli della rete
Lo scopo del cmdlet non è duplicare le impostazioni della rete classica in Resource Manager. Usa le funzionalità di rete nel modo più pratico per la clonazione della macchina virtuale stessa. Ecco cosa accade nelle diverse condizioni:

1.  Nessuna rete virtuale nel gruppo di risorse di destinazione
    - La macchina virtuale di origine non è presente una subnet: viene creata una rete virtuale predefinita con valore 10.0.0.0/16 e una subnet, con uno spazio indirizzi 10.0.0.0/22.
    - La macchina virtuale di origine si trova in una subnet: viene individuata la rete virtuale in cui si trova la macchina virtuale, vengono copiate la specifica della rete virtuale insieme alle subnet.
2.  Il gruppo di risorse di destinazione ha una rete virtuale con nome `<VM virtual network>arm` (viene aggiunta la stringa 'arm')
    - Se la rete virtuale ah una subnet con lo stesso nome e lo stesso spazio di indirizzi, usarli.
    - Se non viene trovata una subnet appropriata, trovare un blocco di indirizzi nelle subnet esistenti con maschera di 22 bit e usarlo.

## Clonazione e migrazione della piattaforma

Esistono alcune differenze tra l'approccio attuale alla clonazione e la migrazione della piattaforma supportata.

### Clonazione


| Vantaggi | Svantaggi |
|--------------------------------------------------------|:----------------------------------------------------------------:|
| Clonazione di una macchina virtuale all'interno di un servizio cloud. | Tempo di inattività delle macchine virtuali perché come devono essere arrestate. |
| Clonazione di tutte le macchine virtuali in una rete virtuale o all'esterno di una rete virtuale. | Ritardi in uno scenario in cui è necessario eseguire una copia dei BLOB del disco. |
| | Modifica nella configurazione di rete per la VM: indirizzi IP interni, indirizzi VIP. |


### Migrazione della piattaforma


| Vantaggi | Svantaggi |
|----------------------------------|:-------------------------------------------:|
| La maggior parte delle configurazioni delle VM all'interno di una rete virtuale non causa tempi di inattività. | Deve essere eseguita la migrazione di tutte le VM in un servizio cloud o della la rete virtuale con tutte le risorse distribuite al suo interno. |
 
 
## Scenari non supportati

**Le operazioni seguenti non rientrano nell'ambito degli script di clonazione**

 1. Arrestare una macchina virtuale in esecuzione.
 2. Modificare dati o dischi.
 3. Clonare VM in esecuzione.
 4. Clonare automaticamente più VM in un scenario complesso.
 5. Clonare l'intera configurazione di rete ASM.
 6. Creare VM con carico bilanciato. Si presuppone che questa sia una configurazione che l'esperto di Azure debba gestire in modo esplicito.
 
 
## Configurazioni testate

Il cmdlet _Add-AzureSMVmToRM_ è stato testato con i test case seguenti:

| # | Descrizione |
|:---|:---|
| 1 | Macchina virtuale Windows con un disco del sistema operativo esistente. |
| 2 | Macchina virtuale Linux con un disco del sistema operativo esistente. |
| 3 | Macchina virtuale Windows con dischi del sistema operativo e dati esistenti. |
| 4 | Macchina virtuale Linux con dischi dati e del sistema operativo esistenti. |
| 5 | Macchina virtuale Windows con un nuovo disco del sistema operativo corrispondente dalla Raccolta immagini. |
| 6 | Macchina virtuale Linux con un nuovo disco del sistema operativo corrispondente dalla Raccolta immagini. |
| 7 | Macchina virtuale Windows con un nuovo disco del sistema operativo e dischi dati vuoti. |
| 8 | Macchina virtuale Linux con un nuovo disco del sistema operativo e dischi dati vuoti. |
| 9 | Macchina virtuale Windows con endpoint pubblici. |
| 10 | Macchina virtuale Linux con endpoint pubblici. |
| 11 | Macchina virtuale Windows con un certificato WinRM. |
| 12 | Macchina virtuale Windows in una rete virtuale e subnet. |
| 13 | Macchina virtuale Linux in una rete virtuale e subnet. |
| 14 | Macchina virtuale Windows con estensioni personalizzate. |
| 15 | Macchina virtuale Windows in un set di disponibilità. |
| 16 | Macchina virtuale Windows in un set di disponibilità, con più dischi dati, endpoint pubblici, in una rete virtuale e subnet e con estensioni personalizzate. |
| 17 | Macchina virtuale Linux in un set di disponibilità, con più dischi dati, endpoint pubblici, in una rete virtuale e subnet e con estensioni personalizzate. |

## Note
1. Se più macchine virtuali vengono clonate una dopo l'altra a intervalli brevi, potrebbero verificarsi conflitti di nomi DNS per gli indirizzi IP pubblici, a causa dei tempi di aggiornamento della cache DNS.

<!---HONumber=AcomDC_0824_2016-->