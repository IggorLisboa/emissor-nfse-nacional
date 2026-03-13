# OBTER PDF NSF-e Nacional

## PDF
Busca o pdf da nota de serviço no Ambiente Nacional.

```php
    ob_start(); 
    error_reporting(E_ALL); 
    ini_set('display_errors', 1);

    require '../../../vendor/autoload.php';
    use Divulgueregional\ApiNfse\NFSeNacional;

    // 1. Configurações e Certificado
    $config = [
        'cert_path' => __DIR__ . '/../certs/cert_empresa.pfx',
        'cert_password' => '123456'
    ];

    $tpAmb = 2; // 2= Homologação, 1= produção
    $nfse = new NFSeNacional($config, $tpAmb);
    $chaveAcesso = '';

    $retorno = $nfse->downloadDANFSe($chaveAcesso);
    // Verificar se ocorreu erro 502 e tentar novamente
    if (isset($retorno['codigo']) && $retorno['codigo'] == '502') {
        // Esperar 1 segundo e tentar novamente
            sleep(1);
            $retorno = $nfse->downloadDANFSe($chaveAcesso);
    }

    $pdfContent = $retorno['pdfContent']; // pdf
    $nomeArquivo = "{$chaveAcesso}.pdf";

    // $modo = 'attachment'; // faz download
    $modo = 'inline'; // abre no navegador

    ob_end_clean();
    header('Content-Type: application/pdf');
    header("Content-Disposition: $modo; filename=\"{$nomeArquivo}\"");

    echo $pdfContent;
    exit;
```