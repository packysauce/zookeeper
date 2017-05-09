
java_binary(
    name='zookeeper-server',
    deps=['//src/java/main:zookeeper'],
    main_class='org.apache.zookeeper.server.quorum.QuorumPeerMain',
    )

java_binary(
    name='zkcli',
    deps=['//src/contrib/fatjar:zookeeper-fat'],
    main_class='org.apache.zookeeper.util.FatJarMain',
    )

copies = []
exports = ['bin', 'conf', 'docs',
           'LICENSE.txt', 'NOTICE.txt', 'README_packaging.txt', 'README.txt',
           'build.xml', 'ivy.xml', 'ivysettings.xml']

for i in exports:
    export_file(i)
    copies.append('cp -r $(location :{}) $D/'.format(i))

for package in MAVEN_PACKAGES:
    copies.append(
        'cp -r $(location //src/java/main:{0}) $D/lib/{0}-{1}.jar'.format(
            package['name'], package['rev'])
    )

# build contrib dir
genrule(
    name='dist-contrib',
    cmd='''
    # GENERATE DIST-CONTRIB
    mkdir -p $OUT && cd $OUT
    cp -r $(location //src/contrib:dist) .
    ''',
    out='contrib',
    )

genrule(
    name='tarball',
    cmd='''
    D="zookeeper-{version}"
    mkdir -p $D/lib
    cp -a $(location //:dist-contrib)/* $D/
    cp -a $(location //src:dist) $D/
    cp $(location //src/java/main:zookeeper) $D/$D.jar
    jar -xf $(location //src:generated-java)
    jar -xf $(location //src/java/main:jutecc-lib)
    find . -name *.class | xargs jar -uf $D/zookeeper-{version}.jar
    {copies}
    tar -czf $OUT $D
    '''.format(version=VERSION, copies='\n    '.join(copies)),
    out='zookeeper-{}.tar.gz'.format(VERSION)
    )
