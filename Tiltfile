SOURCE_IMAGE = os.getenv("SOURCE_IMAGE", default='harbor.donaldforbes.com/aws-tap/source/spring-music-source')
LOCAL_PATH = os.getenv("LOCAL_PATH", default='.')
NAMESPACE = os.getenv("NAMESPACE", default='donald')
OUTPUT_TO_NULL_COMMAND = os.getenv("OUTPUT_TO_NULL_COMMAND", default=' > /dev/null ')

k8s_custom_deploy(
    'spring-music',
    apply_cmd="tanzu apps workload apply -f config/workload.yaml --debug --live-update" +
        " --local-path " + LOCAL_PATH +
        " --source-image " + SOURCE_IMAGE +
        " --namespace " + NAMESPACE +
        " --yes " +
        OUTPUT_TO_NULL_COMMAND + 
        " && kubectl get workload spring-music --namespace " + NAMESPACE + " -o yaml",
    delete_cmd="tanzu apps workload delete -f config/workload.yaml --namespace " + NAMESPACE + " --yes" ,
    deps=['pom.xml', './target/classes'],
    container_selector='workload',
    live_update=[
        sync('./build/classes', '/workspace/BOOT-INF/classes'),
        sync('./src/main/resources/static', '/workspace/BOOT-INF/classes/static')
    ]
)

k8s_resource('spring-music', port_forwards=["8080:8080"],
    extra_pod_selectors=[{'carto.run/workload-name': 'workload-name', 'app.kubernetes.io/component':'run'}])
allow_k8s_contexts('arn:aws:eks:eu-west-2:896659969473:cluster/drf-tap-adapter')