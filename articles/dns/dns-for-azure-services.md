<properties
  pageTitle="Uso del servizio DNS di Azure con altri servizi di Azure | Microsoft Azure"
  description="Informazioni su come usare il servizio DNS di Azure per risolvere la questione dei nomi degli altri servizi di Azure"
  services="dns"
  documentationCenter="na"
  authors="sdwheeler"
  manager="carmonm"
  editor=""
  tags="azure dns"
/>
<tags
  ms.service="dns"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
  ms.date="09/21/2016"
  ms.author="sewhee"
/>


# <a name="using-azure-dns-with-other-azure-services"></a>Uso del servizio DNS di Azure con altri servizi di Azure

Il servizio DNS di Azure è un servizio ospitato di risoluzione nomi e di gestione DNS, che consente di creare i nomi DNS pubblici per le altre applicazioni e gli altri servizi distribuiti in Azure. Creare un nome per un servizio di Azure in un dominio personalizzato è semplice come aggiungere un record del tipo corretto per il servizio.

* Per gli indirizzi IP allocati dinamicamente, è necessario creare un record DNS CNAME che viene associato al nome DNS creato da Azure per il servizio. Gli standard DNS impediscono l'uso di un record CNAME per il vertice della zona.
* Per indirizzi IP statici, è possibile creare un record DNS A usando un nome qualsiasi, incluso un nome _dominio naked_ al vertice della zona.

Nella tabella seguente vengono illustrati i tipi di record supportati che è possibile usare per i vari servizi di Azure. Come si può vedere nella tabella, il servizio DNS di Azure supporta soltanto i record DNS per le risorse di rete con connessione Internet. Il servizio DNS di Azure non può essere usato per la risoluzione nomi degli indirizzi interni e privati.

| Servizio di Azure | Interfaccia di rete | Descrizione |
|---------------|-------------------|-------------|
| Gateway applicazione | IP pubblico front-end | È possibile creare un record DNS A o CNAME. |
| Bilanciamento del carico | IP pubblico front-end | È possibile creare un record DNS A o CNAME. Al servizio di bilanciamento del carico può essere assegnato dinamicamente un indirizzo PI pubblico IPv6. Di conseguenza, è necessario creare un record CNAME per un indirizzo IPv6. |
| Gestione traffico | Nome pubblico | È possibile creare solo un record CNAME che si associa al nome trafficmanager.net assegnato al profilo di Gestione traffico. Per altre informazioni, vedere [Funzionamento di Gestione traffico](../traffic-manager/traffic-manager-how-traffic-manager-works.md#traffic-manager-example). |
| Servizio cloud | IP pubblico | Per gli indirizzi IP allocati staticamente, è possibile creare un record DNS A. Per gli indirizzi IP allocati dinamicamente, è necessario creare un record CNAME che si associa al nome _cloudapp.net_ . Questa regola si applica alle VM create nel portale classico poiché vengono distribuite come un servizio cloud. Per altre informazioni, vedere [Configurare un nome di dominio personalizzato nei servizi cloud](../cloud-services/cloud-services-custom-domain-name-portal.md). |
| Servizio app | IP esterno | Per gli indirizzi IP esterni, è possibile creare un record DNS A. In caso contrario, è necessario creare un record CNAME che viene associato al nome di azurewebsites.net. Per maggiori informazioni, vedere [Eseguire il mapping di un nome di dominio personalizzato in un'app Azure](../app-service-web/web-sites-custom-domain-name.md) |
| VM di Resource Manager | IP pubblico | Le VM di Resource Manager possono avere indirizzi IP pubblici. Una VM con un indirizzo IP pubblico può essere anche dietro a un servizio di bilanciamento del carico. È possibile creare un record DNS A o CNAME per l'indirizzo pubblico. Il nome personalizzato può essere usato per aggirare l'indirizzo VIP sul bilanciamento del carico. |
| Macchine virtuali classiche | IP pubblico | Le VM classiche create con PowerShell o l'interfaccia della riga di comando possono essere configurate con un indirizzo virtuale (riservato) dinamico o statico. È possibile creare rispettivamente un record DNS A o CNAME. |



<!--HONumber=Oct16_HO2-->


