---
title: Creare e configurare un gateway applicazione con il bilanciamento del carico interno (ILB) in una rete virtuale | Microsoft Docs
description: Questa pagina fornisce le istruzioni per configurare un servizio Gateway applicazione di Azure con un endpoint con carico bilanciato interno
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 7403d28e-909f-46a2-b282-43a8e942f53c
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: ee8cfffdbf054b4251ed269745f6b9ee5a5e6c64
ms.openlocfilehash: bb514e1a65fc051d6a36123820c68998bed36fb9


---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Creare un gateway applicazione con un dispositivo di bilanciamento del carico interno (ILB)

> [!div class="op_single_selector"]
> * [PowerShell per Azure classico](application-gateway-ilb.md)
> * [PowerShell per Azure Resource Manager](application-gateway-ilb-arm.md)

Il gateway applicazione può essere configurato con un indirizzo IP virtuale con connessione Internet o con un endpoint interno non esposto a Internet, detto anche endpoint di bilanciamento del carico interno (ILB). Configurare il gateway con un ILB è utile per le applicazioni line-of-business interne non esposte a Internet. È utile anche per i servizi o i livelli in un'applicazione multilivello che si trova entro un limite di sicurezza non esposto a Internet, ma che richiede la distribuzione del carico round robin, la persistenza delle sessioni o la terminazione SSL. Questo articolo illustra la procedura per configurare un gateway applicazione con un ILB.

## <a name="before-you-begin"></a>Prima di iniziare

1. Installare la versione più recente dei cmdlet di Azure PowerShell mediante l'Installazione guidata piattaforma Web. È possibile scaricare e installare la versione più recente dalla sezione **Windows PowerShell** della [pagina di download](https://azure.microsoft.com/downloads/).
2. Assicurarsi di avere una rete virtuale funzionante con una subnet valida.
3. Assicurasi di avere server back-end nella rete virtuale o con un indirizzo IP/VIP pubblico assegnato.

Per creare un nuovo gateway applicazione, eseguire i passaggi seguenti nell'ordine indicato. 

1. [Creare un gateway applicazione](#create-a-new-application-gateway)
2. [Configurare il gateway](#configure-the-gateway)
3. [Definire la configurazione del gateway](#set-the-gateway-configuration)
4. [Avviare il gateway](#start-the-gateway)
5. [Verificare il gateway](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione:

**Per creare il gateway**, usare il cmdlet `New-AzureApplicationGateway` sostituendo i valori correnti con quelli personalizzati. Si noti che la fatturazione per il gateway non viene applicata a partire da questo punto. La fatturazione viene applicata a partire da un passaggio successivo, dopo l'avvio corretto del gateway.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

```
VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error 
----       ----------------     ------------                             ----
Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399
```

**Per confermare** l'avvenuta creazione del gateway è possibile usare il cmdlet `Get-AzureApplicationGateway`. 

Nell'esempio *Description*, *InstanceCount* e *GatewaySize* sono parametri facoltativi. Il valore predefinito per *InstanceCount* è 2, con un valore massimo pari a 10. Il valore predefinito per *GatewaySize* è Medium. Small e Large sono altri valori disponibili. *Indirizzo VIP* e *DnsName* vengono visualizzati come campi vuoti, perché il gateway non si è ancora avviato. Questi valori vengono creati quando il gateway è in esecuzione. 

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 4:39:39 PM - Begin Operation:
Get-AzureApplicationGateway VERBOSE: 4:39:40 PM - Completed 
Operation: Get-AzureApplicationGateway
Name: AppGwTest    
Description: 
VnetName: testvnet1 
Subnets: {Subnet-1} 
InstanceCount: 2 
GatewaySize: Medium 
State: Stopped 
VirtualIPs: 
DnsName:
```

## <a name="configure-the-gateway"></a>Configurare il gateway
La configurazione di un gateway applicazione è costituita da più valori. È possibile collegare tra loro tali valori per creare la configurazione.

I valori possibili sono:

* **Pool di server back-end:** elenco di indirizzi IP dei server back-end. Gli indirizzi IP elencati devono appartenere alla subnet VNet o devono essere indirizzi IP/VIP pubblici. 
* **Impostazioni del pool di server back-end:** ogni pool ha impostazioni come porta, protocollo e affinità basata sui cookie. Queste impostazioni sono associate a un pool e vengono applicate a tutti i server nel pool.
* **Porta front-end:** questa porta è la porta pubblica aperta sul gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
* **Listener:** il listener ha una porta front-end, un protocollo (Http o Https, con applicazione della distinzione tra maiuscole e minuscole) e il nome del certificato SSL (se si configura l'offload SSL). 
* **Regola:** la regola associa il listener e il pool di server back-end e definisce il pool di server back-end a cui deve essere indirizzato il traffico quando raggiunge un listener specifico. È attualmente supportata solo la regola *basic* . La regola *basic* è una distribuzione del carico di tipo round robin.

È possibile definire la configurazione creando un oggetto di configurazione oppure usando un file XML di configurazione. Per definire la configurazione mediante un file XML di configurazione, usare l'esempio seguente.

Tenere presente quanto segue:

* L'elemento *FrontendIPConfigurations* descrive i dettagli ILB pertinenti alla configurazione del gateway applicazione con un ILB. 
* L'elemento *Type* Frontend IP deve essere impostato su "Private".
* *StaticIPAddress* deve essere impostato sull'IP interno desiderato su cui il gateway riceve il traffico. Si noti che l'elemento *StaticIPAddress* è facoltativo. Se non viene impostato, viene scelto un IP interno disponibile dalla subnet distribuita. 
* Il valore dell'elemento *Name* specificato in *FrontendIPConfiguration* deve essere usato nell'elemento *FrontendIP* di HTTPListener per fare riferimento a FrontendIPConfiguration.
  
  **Esempio di file XML di configurazione**
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations>
        <FrontendIPConfiguration>
            <Name>fip1</Name> 
            <Type>Private</Type> 
            <StaticIPAddress>10.0.0.10</StaticIPAddress> 
        </FrontendIPConfiguration>
    </FrontendIPConfigurations>
    <FrontendPorts>
        <FrontendPort>
            <Name>FrontendPort1</Name>
            <Port>80</Port>
        </FrontendPort>
    </FrontendPorts>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>BackendPool1</Name>
            <IPAddresses>
                <IPAddress>10.0.0.1</IPAddress>
                <IPAddress>10.0.0.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>BackendSetting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>HTTPListener1</Name>
            <FrontendIP>fip1</FrontendIP>
            <FrontendPort>FrontendPort1</FrontendPort>
            <Protocol>Http</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>HttpLBRule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
            <Listener>HTTPListener1</Listener>
            <BackendAddressPool>BackendPool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```


## <a name="set-the-gateway-configuration"></a>Definire la configurazione del gateway
Verrà quindi configurato il gateway applicazione. È possibile usare il cmdlet `Set-AzureApplicationGatewayConfig` con un oggetto di configurazione o con un file XML di configurazione. 

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

```
VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
Name       HTTP Status Code     Operation ID                             Error 
----       ----------------     ------------                             ----
Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d
```

## <a name="start-the-gateway"></a>Avviare il gateway

Dopo la configurazione del gateway, usare il cmdlet `Start-AzureApplicationGateway` per avviarlo. La fatturazione per un gateway applicazione verrà applicata a partire dall'avvio corretto del gateway. 

> [!NOTE]
> La regola `Start-AzureApplicationGateway` potrebbe richiedere fino a 15-20 minuti. 
> 
> 

```powershell
Start-AzureApplicationGateway AppGwTest 
```

```
VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error 
----       ----------------     ------------                             ----
Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b
```

## <a name="verify-the-gateway-status"></a>Verificare lo stato del gateway

Usare il cmdlet `Get-AzureApplicationGateway` per verificare lo stato del gateway. Se nel passaggio precedente l'operazione `Start-AzureApplicationGateway` è riuscita, lo stato sarà *In esecuzione* e i valori in Indirizzo VIP e DnsName saranno validi. Questo esempio illustra il cmdlet nella prima riga seguito dall'output. In questo esempio il gateway è in esecuzione ed è pronto per ricevere il traffico. 

> [!NOTE]
> Il gateway applicazione è configurato per accettare il traffico nell'endpoint ILB configurato 10.0.0.10 di questo esempio.

```powershell
Get-AzureApplicationGateway AppGwTest 
```

```
VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway 
VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
Name          : AppGwTest
Description   : 
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Running
VirtualIPs    : {10.0.0.10}
DnsName       : appgw-b2a11563-2b3a-4172-a4aa-226ee4c23eed.cloudapp.net
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni generali sulle opzioni di bilanciamento del carico, vedere:

* [Servizio di bilanciamento del carico di Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gestione traffico di Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)




<!--HONumber=Nov16_HO3-->


