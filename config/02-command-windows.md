# Comandos de configuración de la AWS CLI (Windows)

Los valores entre `< >` se reemplazan por los tuyos. Ejemplo: `<región>` → `us-east-2`.

> Los comandos de este documento están escritos para **PowerShell** (la terminal recomendada en Windows). Los comandos propios de AWS CLI son iguales en PowerShell, CMD y Linux; lo que cambia es la sintaxis de la terminal para rutas, archivos y variables de entorno.

## 1. Instalación

```powershell
# Ver si está instalada y qué versión
aws --version

# Ver dónde está instalada
(Get-Command aws).Source

# También funciona en PowerShell y CMD
where.exe aws
```

Si no está instalada, descarga el instalador oficial de AWS CLI v2 para Windows desde:

https://docs.aws.amazon.com/es_es/cli/latest/userguide/getting-started-install.html

Ejecuta el instalador MSI descargado y sigue los pasos. Después, cierra y vuelve a abrir PowerShell.

```powershell
# Comprobar la instalación
aws --version
```

Para actualizar, descarga y ejecuta el instalador MSI más reciente. El instalador actualiza la instalación existente.

## 2. Configuración inicial

```powershell
# Configurar llaves, región y formato
aws configure

# Ver la configuración actual y de dónde sale cada valor
aws configure list
```

## 3. Comprobar la conexión

```powershell
# ¿Quién soy?
aws sts get-caller-identity

# Solo el número de cuenta
aws sts get-caller-identity --query "Account" --output text

# Solo el ARN
aws sts get-caller-identity --query "Arn" --output text
```

## 4. Región

```powershell
# Ver la región configurada
aws configure get region

# Cambiar la región
aws configure set region <región>

# Usar otra región en un solo comando
aws ec2 describe-instances --region <región>

# Usar otra región en la sesión actual de PowerShell
$env:AWS_REGION = "<región>"

# Quitar la variable de la sesión actual
Remove-Item Env:AWS_REGION
```

## 5. Formato de salida

```powershell
# Ver el formato configurado
aws configure get output

# Cambiar el formato: json, table o text
aws configure set output <formato>

# Usar otro formato en un solo comando
aws iam list-users --output table
```

| Formato | Cuándo usarlo |
|---|---|
| `json` | Por defecto. Muestra todos los datos |
| `table` | Para leer o proyectar en clase |
| `text` | Para copiar un solo valor o usarlo en un script |

## 6. Perfiles

```powershell
# Crear o modificar un perfil
aws configure --profile <perfil>

# Listar los perfiles guardados
aws configure list-profiles

# Ver la configuración de un perfil
aws configure list --profile <perfil>

# Usar un perfil en un solo comando
aws sts get-caller-identity --profile <perfil>

# Usar un perfil durante la sesión actual de PowerShell
$env:AWS_PROFILE = "<perfil>"

# Volver al perfil por defecto en esta sesión
Remove-Item Env:AWS_PROFILE

# Cambiar la región de un perfil
aws configure set region <región> --profile <perfil>
```

## 7. Archivos de configuración

```powershell
# Ver la carpeta y los archivos de AWS
Get-ChildItem "$HOME\.aws"

# Ver la región y el formato de cada perfil
Get-Content "$HOME\.aws\config"

# Ver qué perfiles tienen llaves, sin mostrar las llaves
Select-String -Path "$HOME\.aws\credentials" -Pattern "^\["
```

| Archivo | Qué contiene |
|---|---|
| `$HOME\.aws\credentials` | Las llaves de acceso de cada perfil |
| `$HOME\.aws\config` | La región y el formato de cada perfil |

Para eliminar un perfil, se borra su bloque en ambos archivos.

> No uses `Get-Content "$HOME\.aws\credentials"` mientras compartes pantalla: muestra tus claves secretas.

## 8. Filtrar la salida con --query

```powershell
# Un solo campo
aws sts get-caller-identity --query "Arn"

# Un campo de cada elemento de una lista
aws iam list-users --query "Users[].UserName"

# Varios campos, como tabla
aws iam list-users --query "Users[].[UserName,CreateDate]" --output table

# Solo el primer elemento
aws iam list-users --query "Users[0]"

# Contar elementos
aws iam list-users --query "length(Users)"
```

## 9. Ayuda

```powershell
# Ayuda general
aws help

# Comandos de un servicio
aws iam help

# Opciones de una acción
aws iam create-user help
```

La ayuda se abre en un visor: se sale con la tecla `q`.

## 10. Comodidad

```powershell
# Que las respuestas largas no se abran en un visor
aws configure set cli_pager ""

# Ver qué envía y recibe la CLI, para depurar
aws sts get-caller-identity --debug
```

> `--debug` puede mostrar datos sensibles. No pegues su salida en un chat ni en un repositorio.

## 11. Variables de entorno

Tienen prioridad sobre los archivos de configuración y duran hasta cerrar la sesión de PowerShell.

| Variable | Qué define |
|---|---|
| `AWS_PROFILE` | El perfil a usar |
| `AWS_REGION` | La región a usar |
| `AWS_DEFAULT_OUTPUT` | El formato de salida |

```powershell
# Ver las variables de entorno cuyo nombre empieza por AWS
Get-ChildItem Env:AWS*

# Quitar una variable de la sesión actual (reemplaza <VARIABLE>)
Remove-Item Env:<VARIABLE>
```

Para CMD, los equivalentes básicos son:

```cmd
:: Definir una variable durante esta ventana de CMD
set AWS_PROFILE=<perfil>

:: Quitarla durante esta ventana de CMD
set AWS_PROFILE=

:: Ver las variables AWS definidas
set AWS
```

## 12. Orden de prioridad

Cuando un valor está definido en varios lugares, gana el primero de esta lista:

1. La opción en el comando (`--region`, `--profile`, `--output`).
2. Las variables de entorno.
3. Los archivos `%USERPROFILE%\.aws\credentials` y `%USERPROFILE%\.aws\config`.

## 13. Errores frecuentes

| Error | Causa | Solución |
|---|---|---|
| `aws: command not found` o `aws no se reconoce` | La CLI no está instalada o no está en el PATH | Instalarla y abrir una terminal nueva |
| `Unable to locate credentials` | No se ha corrido `aws configure` | `aws configure` |
| `InvalidClientTokenId` | La llave no es válida o fue borrada | Crear otra llave y reconfigurar |
| `SignatureDoesNotMatch` | La clave secreta está mal copiada | Repetir `aws configure` |
| `NoRegion` | El perfil no tiene región | `aws configure set region <región>` |
| `The config profile (x) could not be found` | El perfil no existe | `aws configure list-profiles` |
| `ParamValidation` | El comando está mal escrito | Revisar opciones y mayúsculas |
