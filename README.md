# Minificar código HTML desde Codeigniter

La minificación se refiere a la eliminación de bytes innecesarios, como los espacios adicionales, saltos de línea y sangrías. Al minimizar los códigos HTML se puede acelerar la carga de una página web.

Podemos utilizar multiples herramientas online, preprocesadores, entre otros para generar una versión optimizada del código HTML. En esta oportunidad permítame mostrarle como minificar nuestro código HTML desde el framework codeigniter; usaremos un método donde eliminaremos espacios en blanco innecesarios del HTML generado, excepto Javascript. Sin más empecemos:

### 1.- Modificar config.php 

En el archivo de configuración `aplication/config/config.php` modificar la variable enable_hooks de `FALSE` a `TRUE`.

``` php
$config['enable_hooks'] = TRUE;
```

### 2.- Modificar hooks.php 

Ubicar los el archivo `aplication/config/hooks.php` y adicionar las siguientes líneas de código:

``` php
$hook['display_override'][] = array(
	'class' => '',
	'function' => 'compress',
	'filename' => 'compress.php',
	'filepath' => 'hooks'
);
```

### 3.- Crear compress.php 

Crear el archivo `aplication/hooks/compress.php` y pegar el siguiente código php:

``` php
<?php  if ( ! defined('BASEPATH')) exit('No direct script access allowed');
function compress()
{
	ini_set("pcre.recursion_limit", "16777");
	$CI =& get_instance();
	$buffer = $CI->output->get_output();

	$re = '%# Collapse whitespace everywhere but in blacklisted elements.
        (?>             # Match all whitespans other than single space.
          [^\S ]\s*     # Either one [\t\r\n\f\v] and zero or more ws,
        | \s{2,}        # or two or more consecutive-any-whitespace.
        ) # Note: The remaining regex consumes no text at all...
        (?=             # Ensure we are not in a blacklist tag.
          [^<]*+        # Either zero or more non-"<" {normal*}
          (?:           # Begin {(special normal*)*} construct
            <           # or a < starting a non-blacklist tag.
            (?!/?(?:textarea|pre|script)\b)
            [^<]*+      # more non-"<" {normal*}
          )*+           # Finish "unrolling-the-loop"
          (?:           # Begin alternation group.
            <           # Either a blacklist start tag.
            (?>textarea|pre|script)\b
          | \z          # or end of file.
          )             # End alternation group.
        )  # If we made it here, we are not in a blacklist tag.
        %Six';

    $new_buffer = preg_replace($re, " ", $buffer);

    // We are going to check if processing has working
	if ($new_buffer === null)
	{
		$new_buffer = $buffer;
	}

	$CI->output->set_output($new_buffer);
	$CI->output->_display();
}
```

### 4.- Verificar cambios.
Guarde los cambios realizados y verifique el código fuente de su página web desde el navegador.

> Si no notas los resultados, 
> por favor borra la cache de tu navegador
> y vuelve a verificar el código fuente.


#### Referencia.

[Compress-HTML-output](https://github.com/bcit-ci/CodeIgniter/wiki/Compress-HTML-output)