<properties
   pageTitle="Località per ExpressRoute"
   description="Questa pagina fornisce una panoramica dettagliata delle località in cui vengono offerti i servizi e informazioni su come connettersi alle aree di Azure."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" /> <tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/29/2015"
   ms.author="cherylmc" />

# Partner e località di peering per ExpressRoute
Questa tabella fornisce informazioni dettagliate sugli elementi seguenti:

1. Provider di connettività per ExpressRoute (EXP e NSP)
2. Copertura geografica per ExpressRoute
3. Servizi cloud Microsoft supportati su ExpressRoute
4. Integratori di sistemi ExpressRoute

## Provider di connettività per ExpressRoute
ExpressRoute è supportato in tutte le aree e le località di Azure. La mappa seguente fornisce un elenco di aree di Azure e località per ExpressRoute. Per località ExpressRoute si intendono le aree in cui è attivo il peering di Microsoft con alcuni provider di servizi.
 
![](./media/expressroute-locations/expressroute-locations-map.png)

Si otterrà l'accesso al servizio di Azure in tutte le aree di un'area geopolitica se è stata effettuata la connessione ad almeno una località per ExpressRoute entro l'area geopolitica. La tabella seguente fornisce una mappa di aree di Azure e di località per ExpressRoute entro un'area geopolitica.

|**Area geopolitica**|**Aree di Azure**|**Località per ExpressRoute**|
|---|---|---|
|**Stati Uniti**|Tutte le aree degli Stati Uniti: Stati Uniti orientali, Stati Uniti occidentali, Stati Uniti orientali 2, Stati Uniti centrali, Stati Uniti centro-meridionali, Stati Uniti centro-settentrionali|Atlanta, Chicago, Dallas, Los Angeles, New York, Seattle, Silicon Valley, Washington DC|
|**Sud America**|Brasile meridionale|Sao Paulo|
|**Europa**|Europa settentrionale, Europa occidentale|Amsterdam, Londra|
|**Asia**|Asia orientale, Asia sudorientale|Hong Kong, Singapore|
|**Giappone**|Giappone occidentale, Giappone orientale|Tokyo|
|**Australia**|Australia sudorientale, Australia orientale|Sydney|

La connettività tra diverse aree geopolitiche non è supportata. È possibile collaborare con il provider di connettività per estendere la connettività in diverse aree geopolitiche usando la rete del provider.


### Località per i provider di Exchange (EXP)
- Per un elenco di provider di Exchange e delle località in cui sono supportati, vedere questa [tabella](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_EXP).
-  Per i passaggi per la configurazione della connessione, vedere [Configurare la connessione EXP](expressroute-configuring-exps.md).

### Località per i provider di servizi di rete
- Per un elenco di provider di servizi di rete e delle località in cui sono supportati, vedere questa [tabella](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_NSP).
- Per i passaggi per la configurazione della connessione, vedere [Configurare la connessione NSP](expressroute-configuring-nsps.md).

### Connettività tramite provider di servizi non elencati in precedenza

Se il provider di connettività usato non è incluso negli elenchi precedenti, sarà comunque possibile creare una connessione.

- Contattare il provider di connettività per verificare se è connesso a provider di Exchange nelle località elencate per EXP. Per altre informazioni sui servizi offerti dai provider di Exchange, vedere i collegamenti seguenti. Alcuni provider di connettività sono già connessi agli scambi Ethernet dei provider di Exchange.
	- [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/) 
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
- Richiedere al provider di connettività di estendere la rete alla località di Exchange scelta.
	- Assicurarsi che il provider di connettività estenda la connettività con disponibilità elevata, in modo che non siano presenti singoli punti di errore.
	- È possibile che i provider di connettività (in particolare i provider Ethernet) richiedano all'utente di ottenere un paio di circuiti per gli scambi Ethernet, in modo da assicurare la disponibilità elevata. 
- Ordinare un circuito ExpressRoute tramite il provider di Exchange per connettersi ad Azure
	- Per configurare la connettività, eseguire la procedura illustrata in [Configurare la connessione EXP](expressroute-configuring-exps.md).

|**Provider di connettività**|**Provider di Exchange**|**Località di peering**|
|---|---|---|
|**[XO Communications](http://www.xo.com/)**|Equinix|Silicon Valley|


## ExpressRoute e Servizi cloud Microsoft
Le tabelle seguenti forniscono informazioni dettagliate sui provider di connettività e un elenco dei servizi cloud Microsoft supportati. Contattare il provider di servizi

**Provider di Exchange (EXP)**

|**Provider di servizi**|**Servizi di Microsoft Azure**|**Servizi di Office 365**|
|---|---|---|
|**Aryaka**|Supportato||
|**Colt Ethernet**|Supportato||
|**Equinix**|Supportato|Imminente|
|**InterCloud**|Supportato||
|**Servizio EVPL Level 3**|Supportato||
|**TeleCity Group**|Supportato||
|**Zayo Group**|Supportato||

**Provider di servizi di rete (NSP)**

|**Provider di servizi**|**Servizi di Microsoft Azure**|**Servizi di Office 365**|
|---|---|---|
|**AT&T**|Supportato|Imminente|
|**British Telecom**|Supportato|Imminente|
|**Colt IPVPN**|Supportato||
|**Internet Initiative Japan Inc. - IIJ**|Supportato||
|**IPVPN Level3**|Supportato||
|**Orange**|Supportato|| 
|**SingTel**|Supportato||
|**Tata Communications**|Supportato||
|**Telstra Corporation**|Supportato||
|**Verizon**|Supportato|| 


## Integratori di sistemi ExpressRoute
L'abilitazione della connettività privata ottimale per le proprie esigenze può risultare complessa, in base alla scala della rete. Per ottenere supporto per l'onboarding in ExpressRoute, è possibile collaborare con uno degli Integratori di sistemi elencati nella tabella seguente.


|**Integratore di sistemi**|**Continente**|
|---|---|
|**[Nimbo](http://www.nimbo.com/)**|Stati Uniti||
|**[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)**|EMEA|

## Passaggi successivi
- Assicurarsi che siano soddisfatti i [prerequisiti per ExpressRoute](expressroute-prerequisites.md).
- Per altre informazioni, vedere la pagina relativa alle [domande frequenti](expressroute-faqs.md).
- Selezionare il provider e configurare la connessione. Per informazioni sulla configurazione, vedere [Configurare la connessione EXP](expressroute-configuring-exps.md) o [Configurare la connessione NSP](expressroute-configuring-nsps.md).
<!--HONumber=54-->