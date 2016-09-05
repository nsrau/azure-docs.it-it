<properties
   pageTitle="Anteprima del supporto di Gestione risorse di Azure per il bilanciamento del carico | Microsoft Azure "
   description="Uso di PowerShell per il bilanciamento del carico con la versione di anteprima di Gestione risorse di Azure. Uso di modelli per il bilanciamento del carico"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="sewhee" />


# Supporto di Gestione risorse di Azure per il bilanciamento del carico

Gestione risorse di Azure rappresenta il nuovo framework di gestione dei servizi in Azure. È ora possibile gestire il bilanciamento del carico di Azure con API e strumenti basati su Gestione risorse di Azure.

## Concetti

Con Gestione risorse di Azure, il bilanciamento del carico di Azure contiene le seguenti risorse figlio:

- Configurazione IP front-end: un bilanciamento del carico può includere uno o più indirizzi IP front-end, anche noti come IP virtuali (indirizzi VIP). Questi indirizzi IP vengono usati come ingresso per il traffico.

- Pool di indirizzi back-end: indirizzi IP associati alle schede di interfaccia di rete della macchina virtuale a cui viene distribuito il carico.

- Regole di bilanciamento del carico: una proprietà della regola esegue il mapping di una specifica combinazione di IP e porte front-end a un set di combinazioni di indirizzi IP e porte back-end. Con una singola definizione di una risorsa di bilanciamento del carico è possibile definire più regole di bilanciamento carico, ciascuna delle quali riflette una combinazione di IP e porte front-end e di IP e porte back-end associata alle macchine virtuali.

- Probe: le probe consentono di tenere traccia dell'integrità delle istanze della macchina virtuale. Se la probe di integrità non riesce, l'istanza della macchina virtuale viene esclusa automaticamente dalla rotazione.

- Regole NAT in ingresso: regole NAT che definiscono il traffico in ingresso che attraversa l'IP front-end e viene distribuito all'IP back-end.


![](./media/load-balancer-arm/load-balancer-arm.png)



## Modelli di Guida introduttiva

Gestione risorse di Azure consente di effettuare il provisioning delle applicazioni usando un modello dichiarativo. In un unico modello, è possibile distribuire più servizi con le relative dipendenze. Si usa lo stesso modello per distribuire più volte l'applicazione durante ogni fase del ciclo di vita dell'applicazione

I modelli includono macchine virtuali, reti virtuali, set di disponibilità, interfacce di rete, account di archiviazione, bilanciamenti del carico, gruppi di sicurezza di rete e IP pubblici. Con i modelli è possibile creare tutto ciò che serve per un'applicazione complessa usando un semplice file che si può archiviare e a cui si può collaborare.

[Altre informazioni sui modelli](http://go.microsoft.com/fwlink/?LinkId=544798)

[Altre informazioni sulle risorse di rete](../virtual-network/resource-groups-networking.md)

I modelli che usano il bilanciamento del carico di Azure sono disponibili in un [repository GitHub](https://github.com/Azure/azure-quickstart-templates) che ospita un set di modelli generati dalla community.

Esempi di modelli:

- [2 macchine virtuali in un bilanciamento del carico e regole di bilanciamento del carico](http://go.microsoft.com/fwlink/?LinkId=544799)

- [2 macchine virtuali in una rete virtuale con un bilanciamento del carico interno e regole di bilanciamento del carico](http://go.microsoft.com/fwlink/?LinkId=544800)

- [2 macchine virtuali in un bilanciamento del carico e regole NAT di configurazione per il bilanciamento del carico](http://go.microsoft.com/fwlink/?LinkId=544801)


## Configurazione del bilanciamento del carico di Azure con PowerShell o l'interfaccia della riga di comando

I [cmdlet di rete di Azure](https://msdn.microsoft.com/library/azure/mt163510.aspx) possono essere usati per creare un bilanciamento del carico. Iniziare a usare i cmdlet di Gestione risorse di Azure e le API REST.

- [Come creare un servizio di bilanciamento del carico tramite Gestione risorse di Azure](load-balancer-get-started-internet-arm-ps.md)

- [Uso dell'interfaccia della riga di comando di Azure con Gestione risorse di Azure](../xplat-cli-azure-resource-manager.md)

- [API REST di bilanciamento del carico](https://msdn.microsoft.com/library/azure/mt163651.aspx)


## Passaggi successivi

È anche possibile [iniziare a creare un servizio di bilanciamento del carico con connessione Internet](load-balancer-get-started-internet-arm-ps.md) e configurare il tipo di [modalità di distribuzione](load-balancer-distribution-mode.md) per il comportamento specifico del traffico di rete per il servizio di bilanciamento del carico.

Se l'applicazione deve mantenere attive le connessioni per i server dietro il servizio di bilanciamento del carico, vedere altre informazioni sulle [impostazioni del timeout di inattività delle connessioni TCP per il servizio di bilanciamento del carico](load-balancer-tcp-idle-timeout.md). Ciò consente di ottenere informazioni sul comportamento delle connessioni inattive quando si usa il servizio di bilanciamento del carico di Azure.

<!---HONumber=AcomDC_0824_2016-->