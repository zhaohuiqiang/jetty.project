node {
  // System Dependent Locations
  def mvntool = tool name: 'maven3', type: 'hudson.tasks.Maven$MavenInstallation'
  def jdktool = tool name: 'jdk7', type: 'hudson.model.JDK'

  // Environment
  List mvnEnv = ["PATH+MVN=${mvntool}/bin", "PATH+JDK=${jdktool}/bin", "JAVA_HOME=${jdktool}/", "MAVEN_HOME=${mvntool}"]
  mvnEnv.add("MAVEN_OPTS=-Xms256m -Xmx1024m -XX:MaxPermSize=512m -Djava.awt.headless=true")

  stage 'Checkout'

  checkout scm

  stage 'Compile'

  withEnv(mvnEnv) {
    sh "mvn -B clean install -Dtest=None"
  }

  stage 'Javadoc'

  withEnv(mvnEnv) {
    sh "mvn -B javadoc:javadoc"
  }

  stage 'Test'

  withEnv(mvnEnv) {
    // Run package then test phase / skip main compilation / ignore failures
    sh "mvn -B package test -Dmaven.main.skip=true -Dmaven.test.failure.ignore=true"
    // Report failures in the jenkins UI
    step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/TEST-*.xml'])
  }
}