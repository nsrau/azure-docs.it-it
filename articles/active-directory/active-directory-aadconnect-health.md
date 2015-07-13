<properties 
	pageTitle="Monitorare l’infrastruttura di identità locale nel cloud." 
	description="Si tratta della pagina di Azure AD Connect Health in cui viene descritto Azure AD Connect Health e il relativo utilizzo." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Monitorare l’infrastruttura di identità locale nel cloud

Azure AD Connect Health consente di monitorare e ottenere informazioni dettagliate sull’infrastruttura di identità locale. Lo strumento offre la possibilità di visualizzare gli avvisi, le prestazioni, i modelli di utilizzo, le impostazioni di configurazione, consente di mantenere una connessione affidabile a Office 365 e molto altro ancora. Tutto ciò viene realizzato mediante un agente installato nei server di destinazione. Per ulteriori informazioni, vedere [Azure AD Connect Health su MSDN.](https://msdn.microsoft.com/library/azure/dn906722.aspx)

![Cos’è Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnecthealth2.png)


Queste informazioni vengono visualizzate tutte nel portale di Azure AD Connect Health. Il portale di Azure AD Connect Health consente di visualizzare gli avvisi, il monitoraggio delle prestazioni e i dati analitici di utilizzo. Queste informazioni vengono visualizzate tutte in un'unica posizione facile da utilizzare, in modo da non sprecare tempo per la ricerca delle informazioni di cui si ha bisogno.

![Cos’è Azure AD Connect Health](./media/active-directory-aadconnect-health/usage.png)

Gli aggiornamenti futuri ad Azure AD Connect Health includeranno ulteriore monitoraggio e informazioni dettagliate su altri componenti di identità e servizi, ad esempio i servizi Azure AD Connect Sync. In tal modo, viene fornito un dashboard unico dal punto di vista delle identità, consentendo di disporre di un ambiente più affidabile, integro e integrato che gli utenti possono sfruttare per aumentare la capacità di svolgere il proprio lavoro.

Per ulteriori informazioni, vedere [Azure AD Connect Health su MSDN.](https://msdn.microsoft.com/library/azure/dn906722.aspx)


![Cos’è Azure AD Connect Health](./media/active-directory-aadconnect-health/logo1.png)




## Perché utilizzare Azure AD Connect Health

L'integrazione delle directory locali con Azure AD rende gli utenti più produttivi in quanto fornisce un'identità comune per accedere alle risorse cloud e locali. Tuttavia, questa integrazione comporta la difficoltà di garantire che questo ambiente sia integro, per consentire agli utenti di accedere in modo affidabile alle risorse locali e cloud da qualsiasi dispositivo. Azure AD Connect Health fornisce un approccio semplice, basato sul cloud, per monitorare e ottenere informazioni dettagliate sull’infrastruttura di identità locale utilizzata per accedere a Office 365 o ad altre applicazioni Azure AD. È semplice come installare un agente in ogni server di identità locale in uso.

Azure AD Connect Health per ADFS supporta AD FS 2.0 in Windows Server 2008/2008 R2, ADFS in Windows Server 2012/2012R2. È inoltre incluso qualsiasi server Proxy ADFS o Proxy applicazione Web Application che fornisce il supporto per l’autenticazione per l’accesso alla rete Extranet. Azure AD Connect Health per ADFS fornisce il seguente insieme di funzionalità chiave:

- Visualizzazione degli avvisi e intervento per un accesso affidabile alle applicazioni di ADFS protette tra cui Azure AD
- Notifiche tramite posta elettronica per gli avvisi critici
- Visualizzazione dei dati sulle prestazioni per determinare la pianificazione della capacità
- Visualizzazioni dettagliate dei modelli di accesso di ADFS per determinare le anomalie o stabilire linee di base per la pianificazione della capacità



----------------------------------------------------------------------------------------------------------
## Download dell’agente di Azure AD Connect Health

Per una guida introduttiva su Azure AD Connect Health, è possibile scaricare la versione più recente dell’agente da qui: [Download dell’agente di Azure AD Connect Health.](http://go.microsoft.com/fwlink/?LinkID=518973) Assicurarsi di aver aggiunto il servizio da Marketplace prima di installare gli agenti.

----------------------------------------------------------------------------------------------------------





**Risorse aggiuntive**

* [Azure AD Connect Health su MSDN](https://msdn.microsoft.com/library/azure/dn906722.aspx)


 

<!---HONumber=62-->