# Secrets

Including static secrets in you nix config is a famously hard problem to solve because of the read-all property of the nix store.

Existing solutions to this problems are tools like [Agenix](https://github.com/ryantm/agenix) or [sops-nix](https://github.com/mic92/sops-nix).
While they are great and achieve maximum security for your nix secrets it is rather time consuming, especially when you have a large fleet.

Imagine a scenario where you have a secret for a cache where all of your hosts need access. When you have `k` administrators and `n` hosts you need to track and maintain all `k * n` hosts.

This gets especially cumbersome in a setup where you want to update the host setup.

## Quickstart

The yeet agent acts as a drop-in replacement for your existing agenix installation. Here is an example:

```nix
yeet.secrets."nix-cache-signing-key" = {
  owner = "nginx";
  group = "nginx";
};

services.mysuperservices.settings.signingKeyFile = config.yeet.secrets."nix-cache-signing-key".path;
```

This will instruct the agent to only activate the derivation if the secret `nix-cache-signing-key` is existent.
To add actual content to this secret you need to use the `yeet secret create` command.
It will guide you through the steps to create this secret and allow your host to access it.

Access to your secret is now managed by the yeetd. You can allow / remove access by using the `yeet secret allow/block` command.

## Reference

```nix
# you probably do not need to set `path` as you can reference this with `config.yeet.secrets."your-secret".path`
path = lib.mkOption {
  default = "${cfg_secret.secretsDir}/${config.name}";
  description = "Path where the decrypted secret is installed.";
};
mode = {
  default = "0400";
  description = "Permissions mode of the decrypted secret in a format understood by chmod.";
};
owner = {
  default = "0";
  description = "User of the decrypted secret.";
};
group = {
  default = lib.users.${config.owner}.group or "0";
  description = "Group of the decrypted secret.";
};
```

## Security implication

Instead of no-one having the secrets now the yeetd is owner of all secrets. Altough they are encrypted at rest and in transit this leaves a single point of failure. It is a clear-cut trade between security and comfort
