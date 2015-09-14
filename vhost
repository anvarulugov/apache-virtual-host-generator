# Python Virtual Host generator script
import sys, os, pwd, grp

# Configs
# enter linux user name
user = 'anvar'
# enter sites directory path
sites_dir = '/home/anvar/sites'
# enter sites http directory name
html_dir = 'public_html'
# enter apache vhosts directory path
apache_hosts = '/etc/apache2/sites-available'

print "VirtualHost generatoriga xush kelibsiz."
print """
Ushbu script apache2 da VirtualHost generatsiya qiladi, buning uchun siz avval scriptni o'zingizga moslab olishingiz va keyin script ishga tushirganda virtual domain nomini kiritishingiz kerak bo'ladi. 
Diqqat: ushbu script ishlashi uchun uni root huquqi bilan ishga tushirishingiz kerak"""

def vhostdata( domain ):
	data = """<VirtualHost *:80>
	DocumentRoot "%(sites_dir)s/%(domain)s/%(html_dir)s/"
	ServerName %(domain)s
	ServerAdmin webmaster@localhost
	<Directory />
		Options FollowSymLinks
		AllowOverride All
	</Directory>
	<Directory %(sites_dir)s/%(domain)s/%(html_dir)s>
		Options Indexes FollowSymLinks MultiViews
		AllowOverride All
		Order allow,deny
		allow from all
		Require all granted
	</Directory>
	ErrorLog %(sites_dir)s/%(domain)s/logs/error.log
	LogLevel warn
	CustomLog %(sites_dir)s/%(domain)s/logs/access.log combined
</VirtualHost>""" % {'domain':domain,'sites_dir':sites_dir,'html_dir':html_dir}
	return data

def vhostcreate(domain):
	if domain:
		confirm = raw_input("Siz kiritgan domain: %s.\n Domainni to'g'ri kiritinganligingizni tasdiqlaysizmi? [y/n]" % domain)
		vhostfile = domain + '.conf'
		if confirm == 'y':
			vhostfile = apache_hosts + '/' + vhostfile
			if os.path.isfile(vhostfile) is True:
				print "%s nomli domain allaqachon qo'shilgan." % domain
				vhostcreate( raw_input('Iltimos, domain nomini kiriting: ') )
			else:
				vhfile = open(vhostfile, 'w')
				vhfile.write(vhostdata(domain))
				vhfile.close()
				print ""
				update_hosts = raw_input('VHost generatsiya qilindi!\n \'hosts\' faylga yangi domain qo\'shilsinmi? [y/n]: ')
				if update_hosts == 'y':
					hostsfile = open('/etc/hosts', 'a')
					hostsfile.write("""127.0.0.1\t%s""" % domain)
					hostsfile.close()				
				generate_home_dir = raw_input('Sayt uchun papkalar ham yaratilsinmi? [y/n]: ')
				if generate_home_dir == 'y':
					home_dir_html = sites_dir + '/' + domain + '/' + html_dir
					home_dir_logs = sites_dir + '/' + domain + '/' + 'logs'
					print "Sayt papkalari quyidagi ko'rinishda generatsiya qilinadi: \n%s \n%s\n agar ushbu manzilda shunday nomlangan papkalar bo'lsa unda yangi papka yaratilmaydi" % (home_dir_html,home_dir_logs)
					if not os.path.exists(home_dir_html):
						# creating http directory
						os.makedirs(home_dir_html)
						# getting user and group id
						uid = pwd.getpwnam(user).pw_uid
						gid = grp.getgrnam(user).gr_gid
						# chowning folders to user
						os.chown(sites_dir + '/' + domain, uid, gid)
						os.chown(home_dir_html, uid, gid)
						# changing http dir chmod to be able to make changes
						os.chmod(home_dir_html,0755)
					if not os.path.exists(home_dir_logs):
						# creating logs directory
						os.makedirs(home_dir_logs)
						# chowning logs folder to user
						os.chown(home_dir_logs, uid, gid)
				os.system("a2ensite %s" % domain)
				os.system("service apache2 reload")
				print "Buyruqlar bajarildi! Iltimos, tekshrib ko'ring."
		else:
			vhostcreate( raw_input('Iltimos, domain nomini kiriting: ') )
	else:
		vhostcreate( raw_input('Iltimos, domain nomini kiriting: ') )

vhostcreate( raw_input('Iltimos, domain nomini kiriting: ') )
