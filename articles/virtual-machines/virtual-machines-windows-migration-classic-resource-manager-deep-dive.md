<properties
	pageTitle="Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello classico al modello di Azure Resource Manager"
	description="Questo articolo include una descrizione tecnica approfondita delle funzionalità del servizio di migrazione supportate dalla piattaforma in Azure Resource Manager"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahthi"/>

# Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello classico al modello di Azure Resource Manager

Questo articolo include un approfondimento tecnico sulla migrazione a livello di risorse e funzionalità per illustrare in che modo la piattaforma esegue la migrazione delle risorse dal modello classico a Resource Manager. Leggere l'articolo relativo all'annuncio del servizio, [Migrazione supportata dalla piattaforma di risorse IaaS dal modello classico al modello di Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md).

## Linee guida dettagliate sulla migrazione a livello di risorse e funzionalità

Di seguito è disponibile la rappresentazione classica e di Resource Manager delle risorse. Funzionalità e risorse non elencate in questa tabella non sono supportato al momento.

| Rappresentazione classica | Rappresentazione in Resource Manager | Note dettagliate | | |
|--------------------------------------------------------|-------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|---|
| Cloud Service Name | Nome DNS | Durante la migrazione, si creerà un nuovo gruppo di risorse per ogni servizio cloud con il modello di denominazione `<cloudservicename>-migrated` che conterrà tutte le risorse. Il nome del servizio cloud diventa un nome DNS associato all'indirizzo IP pubblico. | | |
| Macchina virtuale | Macchina virtuale | Verrà eseguita la migrazione delle proprietà specifiche della VM così come sono. Si noti che alcune informazioni su osProfile, come il nome del computer, non sono state archiviate nel modello di distribuzione classica e quindi rimarranno vuote dopo la migrazione. | | |
| Risorse del disco collegate alla VM | Dischi impliciti collegati alla VM | I dischi non vengono modellati come risorse di primo livello nel modello di distribuzione Resource Manager. Ne verrà eseguita la migrazione come dischi impliciti della VM. Al momento, sono supportati solo i dischi collegati a una VM. La migrazione dei dischi non associati verrà supportata a breve. Per abilitare la migrazione, gli account di archiviazione classica ora possono essere usati dalle VM di Resource Manager. In questo modo è possibile eseguire facilmente la migrazione dei dischi al modello di Resource Manager senza aggiornamenti. | | |
| Estensioni di VM | Estensioni di VM | Verrà eseguita la migrazione di tutte le estensioni di risorse, a eccezione delle estensioni XML dal modello di distribuzione classica. | | |
| Certificati di macchine virtuali | Certificati nell'insieme di credenziali delle chiavi di Azure | Se un servizio cloud contiene i certificati del servizio, la piattaforma crea un nuovo insieme di credenziali delle chiavi di Azure per ogni servizio cloud e sposta i certificati nell'insieme di credenziali delle chiavi. Le VM verranno aggiornate per fare riferimento ai certificati dall'insieme di credenziali delle chiavi. | | |
| Configurazione di WinRM | Configurazione di WinRM in osProfile | La configurazione di WinRM verrà spostata come parte della migrazione. | | |
| Proprietà del set di disponibilità | Risorsa del set di disponibilità | La specifica del set di disponibilità è una proprietà della VM nel modello di distribuzione classica. I set di disponibilità diventano una risorsa di primo livello come parte della migrazione. Si noti che non è supportata la configurazione seguente: "più set di disponibilità per ogni servizio cloud" o "1 o più set di disponibilità 1 insieme a VM non incluse in un set di disponibilità in un servizio cloud". | | |
| Configurazione di rete in una VM | Interfaccia di rete primaria | La configurazione di rete in una VM verrà rappresentata come risorsa di interfaccia di rete primaria dopo la migrazione. Per le VM che non sono in una rete virtuale, l'indirizzo IP interno verrà modificato durante la migrazione. | | |
| Più interfacce di rete in una VM | Interfacce di rete | Se una VM ha più interfacce di rete associate, ognuna diventerà una risorsa di primo livello come parte della migrazione nel modello di distribuzione Resource Manager insieme a tutte le proprietà. | | |
| Set di Endpoint con carico bilanciato | Bilanciamento del carico | Nel modello di distribuzione classica la piattaforma assegna un servizio di bilanciamento del carico implicito per ogni servizio cloud. Durante la migrazione, si creerà una nuova risorsa di bilanciamento del carico e il set di endpoint di bilanciamento del carico diventeranno regole di bilanciamento del carico. | | |
| Regole NAT in ingresso | Regole NAT in ingresso | Gli endpoint di input definiti nella VM verranno vengono convertiti in regole NAT in ingresso nel servizio di bilanciamento del carico durante la migrazione. | | |
| VIP o indirizzo IP virtuale | Indirizzo IP pubblico con nome DNS | L'indirizzo IP virtuale o VIP diventerà un indirizzo IP pubblico e sarà associato al servizio di bilanciamento del carico. | | |
| Rete virtuale | Rete virtuale | Verrà eseguita la migrazione della rete virtuale con tutte le relative proprietà nel modello di distribuzione Resource Manager. Verrà creato un nuovo gruppo di risorse con nome `-migrated`. Per le configurazioni non supportate, vedere [qui](virtual-machines-windows-migration-classic-resource-manager.md) | | |
| IP riservati | Indirizzo IP pubblico con allocationMethod statico | L'indirizzo IP riservato associato al servizio di bilanciamento del carico vengono trasferiti con la migrazione del servizio cloud o della macchina virtuale. La migrazione di indirizzi IP riservati non associata non è supportata al momento. | | |
| Indirizzo IP pubblico per VM | Indirizzo IP pubblico con Allocationmethod dinamico | L'indirizzo IP pubblico associato alla VM viene convertito come risorsa indirizzo IP pubblico con il metodo di allocazione impostato su statico. | | |
| Gruppi di sicurezza di rete | Gruppi di sicurezza di rete | I gruppi di sicurezza di rete associati a una subnet vengono clonati come parte della migrazione al modello di distribuzione Resource Manager. Si noti che il gruppo di sicurezza di rete nel modello di distribuzione classica non viene rimosso durante la migrazione. Tuttavia, le operazioni del piano di gestione per il gruppo di sicurezza di rete verranno bloccate mentre la migrazione è in corso. | | |
| Server DNS | Server DNS | Viene eseguita la migrazione dei server DNS associati a una rete virtuale o alla VM come parte della migrazione delle risorse corrispondenti insieme a tutte le proprietà. | | |
| Route definite dall'utente | Route definite dall'utente | Le route definite dall'utente associate a una subnet vengono clonate come parte della migrazione al modello di distribuzione Resource Manager. Si noti che la route definita dall'utente nel modello di distribuzione classica non viene rimosso durante la migrazione. Tuttavia, le operazioni del piano di gestione per la route definita dall'utente verranno bloccate mentre la migrazione è in corso. | | |
| Proprietà di inoltro IP nella configurazione di rete di una VM | Proprietà di inoltro IP nella scheda di rete | La proprietà di IP in una VM viene convertita in una proprietà nell'interfaccia di rete durante la migrazione. | | |
| Servizio di bilanciamento del carico con più IP | Servizio di bilanciamento del carico con più risorse IP pubblico | Ogni indirizzo IP pubblico associato al servizio di bilanciamento del carico verrà convertito in una risorsa IP pubblico e associato al servizio di bilanciamento del carico al termine della migrazione. | | |
| Nomi DNS interni nella VM | Nomi DNS interni nella scheda di rete | Durante la migrazione i suffissi DNS interni saranno aggiornati in modo da corrispondere ai suffissi del modello di distribuzione Resource Manager. Si noti che la risoluzione dei nomi continuerà a funzionare dopo la migrazione. Tuttavia, i suffissi associati alle VM verranno modificati. | | |

## Illustrazione di esempio di una procedura dettagliata di migrazione semplice

Negli screenshot di esempio illustrati di seguito è possibile vedere la rappresentazione di un servizio cloud con una VM (non in una rete virtuale) dopo la fase di preparazione.

![Screenshot che mostra la rappresentazione classica dopo la preparazione](./media/virtual-machines-windows-migration-classic-resource-manager/classic-migration-prepare-portal.png) ![Screenshot che mostra la rappresentazione Resource Manager dopo la preparazione](./media/virtual-machines-windows-migration-classic-resource-manager/resourcemanager-migration-prepare-portal.png)

## Passaggi successivi

Dopo avere compreso i concetti fondamentali della migrazione di risorse IaaS classiche in Resource Manager, è possibile avviare la migrazione delle risorse.

- [Usare PowerShell per eseguire la migrazione di risorse IaaS dal modello classico al modello di Azure Resource Manager](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [Usare l'interfaccia della riga di comando per eseguire la migrazione di risorse IaaS dal modello classico al modello di Azure Resource Manager](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [Migrazione supportata dalla piattaforma di risorse IaaS dal modello classico al modello di Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
- [Clonare una macchina virtuale classica in Azure Resource Manager usando script PowerShell della community](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0511_2016-->