pipeline
{
     agent
    {
        label 'Master'
        }

    tools
    {
        maven ('maven_3.9.0')
    }
    stages
    {
        stage('SCM')
        {
            steps
            {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/NirmalKumaran94/Maven_War.git']])
            }
        }
        stage('Maven Build')
        {
            steps
            {
                sh 'mvn clean install'
            }

        }
        stage('SonarQube Scan')
        {
            steps
            {
               withSonarQubeEnv("SonarQube")
                {
                    sh "${tool("SonarQube_Ver_4.8")}/bin/sonar-scanner -Dsonar.host.url=http://ec2-15-207-248-63.ap-south-1.compute.amazonaws.com:9000/ -Dsonar.login=sqp_623c2daf041a093cc03d663aaa83e0687d374156 -Dsonar.projectKey=Maven -Dsonar.java.binaries=target"
                }
            }
        }
        stage('Nexus Upload')
        {
            steps
            {
                sh 'mvn -s settings.xml clean deploy'
            }
        }
        stage('deployment')
        {
            agent
            {
                label 'Ansible'
            }
            steps
            {
                sh 'ansible-playbook -i inventory deployment_playbook.yml -e "build_number=${BUILD_NUMBER}"'
            }
        }
    }
}
