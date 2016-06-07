<properties
   pageTitle="Creare un set di record e i record per una zona DNS con il portale di Azure | Microsoft Azure"
   description="Come creare i record host per DNS di Azure e i set di record e i record usando il portale di Azure"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/06/2016"
   ms.author="cherylmc"/>



# Creare set di record e record DNS mediante il portale di Azure


> [AZURE.SELECTOR]
- [Portale di Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Interfaccia della riga di comando di Azure](dns-getstarted-create-recordset-cli.md)


Questo articolo illustra la creazione di record e set di record mediante il portale di Azure. Dopo aver creato la zona DNS, è necessario aggiungere i record DNS per il dominio. A questo scopo, occorre comprendere prima di tutto i record e i set di record DNS.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]


## Creare un nuovo set di record e un record

L'esempio seguente fornisce indicazioni su come creare un set di record e record con il portale di Azure. Viene usato il tipo di record DNS 'A'.

1. Accedere al portale di Azure.

2. Passare al pannello **Zona DNS** in cui creare un set di record.

3. Nella parte superiore del pannello della zona DNS fare clic su **Set di record** per aprire il pannello **Aggiungi set di record**.
 
	![nuovo set di record](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. Nel pannello **Aggiungi set di record** assegnare un nome al set di record, ad esempio, "**www**".
  
	![aggiungere un set di record](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. In Tipo selezionare il tipo di record da creare. Ad esempio, **A**.

6. Impostare **TTL**. L'impostazione predefinita nel portale è di 1 ora.

7. Aggiungere gli indirizzi IP, un indirizzo IP per riga. Se si usa il nome del set di record suggerito e il tipo di record precedente, vengono aggiunti gli indirizzi IP IPv4 al record A del set di record www.

8. Dopo avere aggiunto gli indirizzi IP, fare clic su **OK** nella parte inferiore del pannello. Verrà creato il set di record DNS.


## Passaggi successivi

Per gestire i set di record e i record, vedere le informazioni su [come creare e gestire record e set di record DNS mediante il portale di Azure](dns-operations-recordsets-portal.md).

Per altre informazioni sul servizio DNS di Azure, vedere la [panoramica del servizio DNS di Azure](dns-overview.md).

<!-----HONumber=AcomDC_0518_2016-->