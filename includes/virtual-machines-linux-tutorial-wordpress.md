## <a name="install-wordpress"></a>Installare WordPress

Per provare lo stack, installare un'app di esempio. Come esempio, con i passaggi seguenti viene installata la piattaforma open source [WordPress](https://wordpress.org/) per creare siti Web e blog. Altri carichi di lavoro da provare includono [Drupal](http://www.drupal.org) e [Moodle](https://moodle.org/). 

Questa installazione di WordPress è utilizzabile solo per il modello di verifica. Per installare la versione più recente di WordPress in un ambiente di produzione con le impostazioni di sicurezza consigliate, vedere la [documentazione di WordPress](https://codex.wordpress.org/Main_Page). 



### <a name="install-the-wordpress-package"></a>Installare il pacchetto WordPress

Eseguire il comando seguente:

```bash
sudo apt install wordpress
```

### <a name="configure-wordpress"></a>Configurazione di WordPress

Configurare WordPress per usare MySQL e PHP.

In una directory di lavoro creare un file di testo `wordpress.sql` per configurare il database MySQL per WordPress: 

```bash
sudo sensible-editor wordpress.sql
```

Aggiungere i comandi seguenti, sostituendo *yourPassword* con una password del database di propria scelta. Lasciare invariati gli altri valori. Se in precedenza sono stati configurati criteri di sicurezza MySQL per convalidare la complessità della password, verificare che la password soddisfi i requisiti di complessità. Salvare il file.

```sql
CREATE DATABASE wordpress;
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER
ON wordpress.*
TO wordpress@localhost
IDENTIFIED BY 'yourPassword';
FLUSH PRIVILEGES;
```

Eseguire il comando seguente per creare il database:

```bash
cat wordpress.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf
```

Poiché il file `wordpress.sql` contiene le credenziali del database, eliminarlo dopo l'uso:

```bash
sudo rm wordpress.sql
```

Per configurare PHP, usare il comando seguente per aprire un editor di testo di propria scelta e creare il file `/etc/wordpress/config-localhost.php`:

```bash
sudo sensible-editor /etc/wordpress/config-localhost.php
```
Copiare le righe seguenti nel file, sostituendo *yourPassword* con la password del database di WordPress. Lasciare invariati gli altri valori. Salvare quindi il file.

```php
<?php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpress');
define('DB_PASSWORD', 'yourPassword');
define('DB_HOST', 'localhost');
define('WP_CONTENT_DIR', '/usr/share/wordpress/wp-content');
?>
```


Spostare l'installazione di WordPress nella radice dei documenti del server Web:

```bash
sudo ln -s /usr/share/wordpress /var/www/html/wordpress

sudo mv /etc/wordpress/config-localhost.php /etc/wordpress/config-default.php
```

È ora possibile completare la configurazione di WordPress e pubblicare sulla piattaforma. Aprire un browser e passare a `http://yourPublicIPAddress/wordpress`. Sostituire l'indirizzo IP pubblico della VM. Dovrebbe avere un aspetto simile a questa immagine.

![Pagina di installazione di WordPress](./media/virtual-machines-linux-tutorial-wordpress/wordpressstartpage.png)