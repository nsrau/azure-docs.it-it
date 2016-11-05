
<!--
includes/sql-database-include-ip-address-22-v12portal.md

Latest Freshness check:  2016-03-21 , daleche.

As of circa 2015-09-04, the following topics might include this include:
articles/sql-database/sql-database-configure-firewall-settings.md
articles/sql-database/sql-database-connect-query.md


## Server-level firewall rules

### Add a server-level firewall rule through the new Azure portal
-->


1. Accedere al [portale di Azure](https://portal.azure.com/) all’indirizzo http://portal.azure.com/.
2. Nell'intestazione di sinistra, fare clic su **ESPLORA TUTTO**. Il pannello **Sfoglia** viene visualizzato.
3. Scorrere e fare clic su **Server SQL**. Il pannello **istanze di SQL Server** viene visualizzato.
   
    ![Trovare il server di Database SQL di Azure nel portale di][b21-FindServerInPortal]
4. Per praticità, ridurre a icona i pannelli precedenti nel pannello **Sfoglia**.
5. Nella casella di testo di filtro, iniziare a digitare il nome del server. Viene visualizzata la riga.
6. Fare clic sulla riga per il server. Viene visualizzato un pannello per il server.
7. Nel pannello del server, fare clic su **impostazioni**. Il pannello **impostazioni** viene visualizzato.
8. Fare clic su **Firewall**. Il pannello **Impostazioni del Firewall** viene visualizzato.
   
    ![Fare clic su Impostazioni > Firewall][b31-SettingsFirewallNavig]
9. Fare clic su **aggiungere Client IP**. Digitare un nome per la nuova regola nella prima casella di testo.
10. Digitare i valori di indirizzo IP minimo e massimo per l'intervallo che si desidera abilitare.
    
    * Può essere utile disporre la fine del valore minimo con **0** e del massimo con **255**.
    
    ![Aggiungere un intervallo di indirizzi IP per consentire][b41-AddRange]
11. Fare clic su **Save**.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->

<!---HONumber=AcomDC_0330_2016-->