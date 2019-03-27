---
title: Creare una zona privata di DNS di Azure con l'interfaccia della riga di comando di Azure
description: In questa esercitazione verranno creati e testati una zona privata e un record DNS in DNS di Azure. Questa guida dettagliata illustra come creare e gestire la prima zona DNS privata e il primo record usando l'interfaccia della riga di comando di Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 2758817d58fdd2e80b302b5f833308dbde1a6b63
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779165"
---
# <a name="create-an-azure-dns-private-zone-using-the-azure-cli"></a>Creare una zona privata di DNS di Azure con l'interfaccia della riga di comando di Azure

Questa esercitazione illustra i passaggi per creare la prima zona DNS privata e il primo record con l'interfaccia della riga di comando di Azure.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Una zona DNS viene usata per ospitare i record DNS per un particolare dominio. Per iniziare a ospitare il dominio in DNS di Azure, è necessario creare una zona DNS per il nome di dominio. Ogni record DNS per il dominio viene quindi creato all'interno di questa zona DNS. Per pubblicare una zona DNS privata nella rete virtuale, specificare l'elenco di reti virtuali autorizzate a risolvere i record nella zona.  Tali reti vengono definite *reti virtuali di risoluzione*. È anche possibile specificare una rete virtuale per cui DNS di Azure manterrà i record dei nomi host ogni volta che una macchina virtuale viene creata o eliminata oppure ne viene modificato l'indirizzo IP.  Tale rete viene definita *rete virtuale di registrazione*.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una zona DNS privata
> * Creare le macchine virtuali di test
> * Creare un record DNS aggiuntivo
> * Testare la zona privata

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Se si preferisce, è possibile completare questa esercitazione usando [Azure PowerShell](private-dns-getstarted-powershell.md).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse

Innanzitutto, creare un gruppo di risorse per contenere la zona DNS: 

```azurecli
az group create --name MyAzureResourceGroup --location "East US"
```

## <a name="create-a-dns-private-zone"></a>Creare una zona DNS privata

Una zona DNS viene creata tramite l'uso del comando `az network dns zone create` insieme a un valore *Private* per il parametro **ZoneType**. L'esempio seguente crea una zona DNS denominata **private.contoso.com** nel gruppo di risorse denominato **MyAzureResourceGroup** e rende la zona DNS disponibile per la rete virtuale denominata **MyAzureVnet**.

Se il parametro **ZoneType** viene omesso, la zona viene creata come zona pubblica, quindi questo parametro è necessario per creare una zona privata.

```azurecli
az network vnet create \
  --name myAzureVNet \
  --resource-group MyAzureResourceGroup \
  --location eastus \
  --address-prefix 10.2.0.0/16 \
  --subnet-name backendSubnet \
  --subnet-prefixes 10.2.0.0/24

az network dns zone create -g MyAzureResourceGroup \
   -n private.contoso.com \
  --zone-type Private \
  --registration-vnets myAzureVNet
```

Se si vuole creare una zona solo per la risoluzione dei nomi (senza la creazione automatica dei nomi host), è possibile usare il parametro *resolution-vnets* anziché il parametro *registration-vnets*.

> [!NOTE]
> Non sarà possibile visualizzare i record dei nomi host creati automaticamente. Tuttavia, in un secondo momento verrà eseguito un test per verificare che esistano.

### <a name="list-dns-private-zones"></a>Elencare le zone DNS private

Per enumerare le zone DNS, usare `az network dns zone list`. Per altre informazioni, vedere `az network dns zone list --help`.

Se si specifica il gruppo di risorse, vengono elencate solo le zone all'interno del gruppo di risorse:

```azurecli
az network dns zone list \
  --resource-group MyAzureResourceGroup
```

Se invece il gruppo di risorse viene omesso, sono elencate tutte le zone nella sottoscrizione:

```azurecli
az network dns zone list 
```

## <a name="create-the-test-virtual-machines"></a>Creare le macchine virtuali di test

A questo punto, creare due macchine virtuali in modo da testare la zona DNS privata:

```azurecli
az vm create \
 -n myVM01 \
 --admin-username test-user \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --image win2016datacenter

az vm create \
 -n myVM02 \
 --admin-username test-user \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --image win2016datacenter
```

Questa operazione richiederà qualche minuto.

## <a name="create-an-additional-dns-record"></a>Creare un record DNS aggiuntivo

Per creare un record DNS, usare il comando `az network dns record-set [record type] add-record`. Per informazioni, ad esempio per l'aggiunta di record A, vedere `azure network dns record-set A add-record --help`.

 L'esempio seguente crea un record con il nome relativo **db** nella zona DNS **private.contoso.com** nel gruppo di risorse **MyAzureResourceGroup**. Il nome completo del set di record è **db.private.contoso.com**. Il tipo di record è "A", con indirizzo IP "10.2.0.4".

```azurecli
az network dns record-set a add-record \
  -g MyAzureResourceGroup \
  -z private.contoso.com \
  -n db \
  -a 10.2.0.4
```

### <a name="view-dns-records"></a>Visualizzare i record DNS

Per elencare i record DNS nella zona, eseguire:

```azurecli
az network dns record-set list \
  -g MyAzureResourceGroup \
  -z private.contoso.com
```
Tenere presente che non verranno visualizzati i record creati automaticamente per le due macchine virtuali di test.

## <a name="test-the-private-zone"></a>Testare la zona privata

A questo punto è possibile testare la risoluzione dei nomi per la zona privata **private.contoso.com**.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Configurare le macchine virtuali per consentire il traffico ICMP in entrata

È possibile usare il comando ping per testare la risoluzione dei nomi. Configurare quindi il firewall in entrambe le macchine virtuali per consentire i pacchetti ICMP in ingresso.

1. Connettersi a myVM01 e aprire una finestra di Windows PowerShell con privilegi di amministratore.
2. Eseguire il comando seguente:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Ripetere l'operazione per myVM02.

### <a name="ping-the-vms-by-name"></a>Effettuare il ping delle macchine virtuali in base al nome

1. Dal prompt dei comandi di Windows PowerShell in myVM02, effettuare il ping di myVM01 usando il nome host registrato automaticamente:
   ```
   ping myVM01.private.contoso.com
   ```
   Verrà visualizzato un output simile al seguente:
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Ora effettuare il ping del nome **db** creato in precedenza:
   ```
   ping db.private.contoso.com
   ```
   Verrà visualizzato un output simile al seguente:
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Eliminare tutte le risorse

Quando non è più necessario, eliminare il gruppo di risorse **MyAzureResourceGroup** per eliminare le risorse create in questa esercitazione.

```azurecli
az group delete --name MyAzureResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come distribuire una zona DNS privata, creare un record DNS e testare la zona.
A questo punto, è possibile ottenere maggiori informazioni sulle zone DNS private.

> [!div class="nextstepaction"]
> [Uso di DNS di Azure per i domini privati](private-dns-overview.md)
