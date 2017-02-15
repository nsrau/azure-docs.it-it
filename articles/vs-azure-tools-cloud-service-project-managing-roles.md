---
title: Gestione dei ruoli nei progetti servizio cloud di Azure con Visual Studio | Documentazione Microsoft
description: Informazioni su come aggiungere nuovi ruoli al progetto di servizio cloud di Azure o rimuovere quelli esistenti con Visual Studio.
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 5ec9ae2e-8579-4e5d-999e-8ae05b629bd1
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8c805f9af3154e46f25a6d24c7df33f390189340


---
# <a name="managing-roles-in-the-azure-cloud-services-projects-with-visual-studio"></a>Gestione dei ruoli nei progetti di Servizi cloud di Azure con Visual Studio
Dopo aver creato il progetto di servizio cloud di Azure, è possibile aggiungervi nuovi ruoli o rimuovere quelli esistenti. È possibile anche importare un progetto esistente e convertirlo in un ruolo. Ad esempio, è possibile importare un'applicazione Web ASP.NET e specificarla come ruolo Web.

## <a name="adding-or-removing-roles"></a>Aggiunta o rimozione di ruoli
**Per aggiungere un ruolo**

In **Esplora soluzioni** aprire il menu di scelta rapida per il nodo **Ruoli** nel progetto servizio cloud e scegliere **Aggiungi**. È possibile selezionare un ruolo Web o di lavoro esistente dalla soluzione corrente o crearne di nuovi. In alternativa, è possibile selezionare un progetto appropriato, ad esempio un progetto di applicazione Web ASP.NET, e associarlo a un progetto di ruolo.

**Per rimuovere un'associazione del ruolo**

Nel nodo **Ruoli** del progetto servizio cloud in Esplora soluzioni aprire il menu di scelta rapida per il ruolo da rimuovere e scegliere **Rimuovi**.

## <a name="removing-and-adding-roles-in-your-cloud-service"></a>Rimozione e aggiunta di ruoli nel servizio cloud
Se si rimuove un ruolo dal progetto di servizio cloud ma in un secondo momento si decide di aggiungere nuovamente tale ruolo al progetto, verranno aggiunti solo la dichiarazione del ruolo e gli attributi di base, ad esempio gli endpoint e le informazioni di diagnostica. Nel file ServiceDefinition.csdef o ServiceConfiguration.cscfg non vengono inclusi risorse o riferimenti aggiuntivi. Se si vuole aggiungere queste informazioni, è necessario aggiungerle nuovamente a questi file in modo manuale.

Ad esempio, è possibile rimuovere un ruolo del servizio Web e poi decidere di aggiungere di nuovo questo ruolo nella soluzione. Se si esegue questa operazione, si verificherà un errore. Per impedire questo errore, è necessario aggiungere nuovamente nel file ServiceDefinition.csdef l'elemento `<LocalResources>` mostrato nel codice XML seguente. Usare il nome del ruolo del servizio Web aggiunto di nuovo al progetto come parte dell'attributo nome per l'elemento **<LocalStorage>** . In questo esempio il nome del ruolo del servizio Web è **WCFServiceWebRole1**.

    <WebRole name="WCFServiceWebRole1">
        <Sites>
          <Site name="Web">
            <Bindings>
              <Binding name="Endpoint1" endpointName="Endpoint1" />
            </Bindings>
          </Site>
        </Sites>
        <Endpoints>
          <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
          <Import moduleName="Diagnostics" />
        </Imports>
       <LocalResources>
          <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
       </LocalResources>
    </WebRole>

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su come configurare i ruoli in Visual Studio, vedere [Configurare i ruoli di un servizio cloud di Azure con Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).




<!--HONumber=Nov16_HO3-->


