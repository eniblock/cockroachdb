#!/usr/bin/env python
secret_settings(disable_scrub=True)
config.define_bool("no-volumes")
cfg = config.parse()

clk_k8s = 'clk --force-color k8s -c ' + k8s_context() + ' '

if config.tilt_subcommand == 'up':
    local(clk_k8s + 'add-domain crdb.localhost')

k8s_yaml(
    helm(
        './cockroachdb',
        values=['./cockroachdb/values-dev.yaml'],
        name="cockroachdb",
    )
)
k8s_resource('cockroachdb', port_forwards=['8080:8080', '5432:5432'])

local_resource('helm lint',
               'earthly +lint',
               './cockroachdb/', allow_parallel=True)

if config.tilt_subcommand == 'down' and not cfg.get("no-volumes"):
    local('kubectl --context ' + k8s_context() + ' delete pvc --selector=app.kubernetes.io/instance=cockroachdb --wait=false')
