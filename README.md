# Atualização do App — Processo passo a passo

> Documento: guia moderno e objetivo para atualizar a versão do seu app ajustando diretamente o arquivo **`api.php`** no servidor.

---

## Resumo rápido

1. Conectar ao servidor da API via **WinSCP**.
2. Editar o arquivo `api.php` e atualizar os valores de versão, URL, descrição e flag de atualização obrigatória.
3. Buildar (compilar) o app localmente com essa nova versão.
4. Publicar o novo build e atualizar o link de download no código.

---

## Pré-requisitos

- IP e credenciais de acesso ao servidor da API.
- WinSCP instalado (ou outro cliente SFTP/SSH).
- Permissão para editar arquivos no servidor.
- Ambiente de build configurado (Flutter/Android SDK/Xcode etc.).

---

## 1) Conectar ao servidor e atualizar `api.php`

**Objetivo:** informar à API qual é a versão atual do app e o link de download atualizado.

### Passos

1. Abra o **WinSCP** e configure uma nova conexão:
   - Protocolo: SFTP (ou FTP, se for o caso)
   - Host/IP: `XXX.XXX.XXX.XXX` (o IP do servidor da API)
   - Usuário e senha fornecidos pelo administrador.

2. Navegue até a pasta do projeto da API (ex.: `/var/www/api/routes/`).

3. Localize o arquivo **`api.php`** e clique em **Editar**.

4. Encontre a rota responsável por retornar a versão:

```php
Route::get('/app/vh/versao-atual', function () {
    return response()->json([
        'versao' => '1.1.16',
        'url_apk' => 'https://github.com/tivh/app-release/releases/download/1.1.0/app-release.apk',
        'descricao' => 'RELEASE COM MELHORIAS PARA BUGS.',
        'atualizacao_obrigatoria' => false,
    ]);
});
```

5. Atualize os valores para refletirem a nova versão:

```php
Route::get('/app/vh/versao-atual', function () {
    return response()->json([
        'versao' => '1.2.0',
        'url_apk' => 'https://github.com/tivh/app-release/releases/download/1.2.0/app-release.apk',
        'descricao' => 'Nova versão com melhorias de performance e correções.',
        'atualizacao_obrigatoria' => false,
    ]);
});
```

6. Salve o arquivo. O WinSCP fará o upload automaticamente para o servidor.

7. Reinicie a aplicação/API se necessário. Em Laravel:

```bash
php artisan route:clear
php artisan config:clear
```

**Verificação:** acesse a rota `/api/app/vh/versao-atual` pelo navegador ou Postman e confirme se os dados estão atualizados.

---

## 2) Buildar o app na nova versão

**Objetivo:** gerar o artefato do app atualizado.

- Atualize o versionamento no projeto (`pubspec.yaml` no Flutter, `build.gradle` no Android ou `Info.plist` no iOS).
- Execute o build em modo release:

```bash
flutter build appbundle --release
flutter build apk --release
```

- Teste o app rapidamente em um dispositivo/emulador.

---

## 3) Atualizar o app na URL de download da release

**Objetivo:** disponibilizar o novo build para os usuários.

- Publique o build (APK/AAB/IPA) na plataforma usada:
  - GitHub Releases (recomendado)
  - Servidor de downloads (via WinSCP ou S3)

- Copie a URL do novo artefato (ex.: do GitHub) e atualize no campo `url_apk` dentro do `api.php`.

- Teste acessando o link público.

---

## Checklist rápido ✅

- [ ] `api.php` atualizado com a nova versão, link e descrição.
- [ ] API reiniciada/cache limpo.
- [ ] Build gerado localmente com a versão correta.
- [ ] Artefato publicado na release/servidor.
- [ ] Link público testado.

---

## Rollback rápido

Se algo der errado:

1. Abra novamente o `api.php` no servidor via WinSCP.
2. Volte `'versao'` e `'url_apk'` para os valores anteriores.
3. Salve o arquivo e limpe cache de rotas/configuração.

---

*Gerado automaticamente — guia prático para updates rápidos e seguros editando o `api.php`.*

