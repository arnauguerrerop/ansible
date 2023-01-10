# Borrar logs antiguos



## Objetivo

Este playbook tiene como objetivo hacer una limpieza de logs antiguos con la finalidad de poder liberar espacio en el disco.

La antigüedad de los ficheros que van a ser eliminados está definida en una variable para poder ser modificada según necesidad.

Se recomienda consultar la importancia de los logs antes de lanzar el playbook y eliminarlos permanentemente.

  

## Estructura del playbook

```
-
	limpiar_logs.yml
	README.md
	roles
		- borrar_logs
			- tasks
				main.yml
			- vars
				main.yml
```
  
  

## Variables del rol

El rol contiene una serie de variables para definir tanto el directorio donde se realizará la búsqueda y borrado de los ficheros de logs, como la antigüedad de los ficheros que van a ser eliminados.    


#### Variables:

  
```
path: /var/log/
dias: 30d
```

  

## Playbook principal

```
-
	name: Borrar logs antiguos
	hosts: all
	tasks:
	
	# Playbook principal desde el cual se hace una llamada al rol 'borrar_logs'.

		-
			name:
			import_role:
				name: borrar_logs

 ```

  

### Task principal

```
	# Realiza una búsqueda de logs en un directorio definido en una varialbe y con una antiguedad definida en otra variable, y almacena los resultados en la variable 'logs'.
-
	name: Buscar logs antiguos
	find:
		paths: "{{ path }}"
		patterns:
			- '*.log'
		age: "{{ age }}"
		recurse: yes
	register: logs

  
	# Recorre los ficheros almacenados en la variable logs, y los borra.
-
	name: Borrar logs antiguos
	file:
		path: "{{ item.path }}"
		state: absent
	with_items: "{{ logs.file }"
