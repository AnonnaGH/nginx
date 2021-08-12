# Galaxyshopbd.com Server setup

## Old server service location
> /srv/mastererp/galaxyshopbd.com
> g@l@xySh0pbd


## Mysql Export database
> mysql -u root -p\
> Most$aMa2120Ar!$hA\
> mysqldump -u root -p fdl_ecommerce > fdl_ecommerce.sql

## Database copy to new server using ssh scp
> scp fdl_ecommerce.sql root@209.188.21.162:/root

## Copy data directory from old to new server
> scp -r /srv/mastererp/galaxyshopbd.com/data root@209.188.21.162:/home/mastererp/dev.galaxyshopbd.com


## nginx virtual hosting configuration
> nano /etc/nginx/conf.d/galaxyshobd.com.conf

```
server {
        listen 80;
        server_name galaxyshopbd.com www.galaxyshopbd.com;
	    #expires 1d;

	location / {

          #proxy_cache my_cache;
	      #proxy_buffering        on;
	      #proxy_cache_valid      200  1d;
	      #proxy_cache_use_stale  error timeout invalid_header updating
          #http_500 http_502 http_503 http_504;

          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header X-Forwarded-Proto $scheme;
          proxy_set_header X-Real-IP $remote_addr;
	      proxy_set_header Host $host;
          proxy_pass  http://127.0.0.1:8081;
        }
}
```

## Obtaining SSL certificate from letsencrypt
> sudo certbot --nginx -d galaxyshopbd.com -d www.galaxyshopbd.com


## Database migration
> mysql -u root -p\
> M@teOr$2021

## Delete previous database
> DROP DATABASE fdl_ecommerce; 

## Create new database
> CREATE DATABASE fdl_ecommerce;

## Database Import using command line
> mysql -u root-p fdl_ecommerce < fdl_ecommerce.sql
 
## Creating index for MySQL Table
> `visitor_session table (1.1 million records)`\
> CREATE INDEX vsession_code_idx ON visitor_session (cid,session_code,status);

> CREATE INDEX login_auth_idx ON login (username, access_id, status);

> CREATE INDEX sales_order_idx ON doc_keeper(account_id, cid, doc_type, doc_status,posting_date,status, doc_id);

> CREATE INDEX sales_order_idx2 ON doc_keeper(cid, doc_type, doc_status,posting_date,status,doc_id);

> CREATE INDEX area_mall_idx ON area_shopping_mall(cid,`status`,area_name);

> CREATE INDEX area_shp_idx ON area_shopping_mall(cid,city_id,`status`);

> CREATE INDEX retailer_sre_idx ON retailer(cid,city_id,area_shopping_mall,`status`,retailer_name);

> CREATE INDEX chk_shp_idx ON doc_payship_info(cid,doc_number,`status`);

> CREATE INDEX pick_shp_idx ON shipping_option(remarks,status);

> CREATE INDEX local__pick_idx ON local_pickup(doc_number,`status`);

## SQL long join query sample

> `SELECT l.loc_id,d.doc_number,l.remarks
		,a.area_name,a.area_id
		,r.retailer_id,r.retailer_name,r.address,r.cell_number
		,d.shipping_method,s.shipop_id,count(l.loc_id)as local_pickup_row_count
		FROM doc_payship_info d
		LEFT JOIN local_pickup l ON d.doc_number=l.doc_number AND l.status=0
		LEFT JOIN area_shopping_mall a ON a.area_id=l.area_id
		LEFT JOIN retailer r ON r.retailer_id=l.retailer_id
		LEFT JOIN shipping_option s ON LOCATE('retailer',s.remarks) AND s.status=1
		where d.cid=1 and d.doc_number='CARTDF523A41' AND d.status=0;`


## Mysql Error 1140 and Resolution

> `Error Code: 1140. In aggregated query without GROUP BY, expression #1 of SELECT list contains nonaggregated column 'fdl_ecommerce.l.loc_id'; this is incompatible with sql_mode=only_full_group_by`

> sql_mode= ""

## Configure mysql file
> nano /etc/mysql/mysql.conf.d/mysqld.cnf

```
[mysqld]
pid-file	= /var/run/mysqld/mysqld.pid
socket		= /var/run/mysqld/mysqld.sock
datadir		= /var/lib/mysql
log-error	= /var/log/mysql/error.log

#bind-address = 0.0.0.0

max_connections = 1001
connect_timeout = 6000
net_read_timeout = 600

sql_mode= ""
```

## Check the server ip using curl
> curl -4 https://icanhazip.com (Ipv4)\
> curl -6 https://icanhazip.com (Ipv6)


# Resources
* [How to genereate SSH key](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-centos-8)