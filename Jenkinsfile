pipeline {

  agent none

  stages{
      stage("worker-build"){
        when{
            changeset "**/worker/**"
          }

        agent{
          docker{
            image 'maven:3.6.1-jdk-8-slim'
            args '-v $HOME/.m2:/root/.m2'
          }
        }

        steps{
          echo 'Compiling worker app..'
          dir('worker'){
            sh 'mvn compile'
          }
        }
      }
      stage("worker-test"){
        when{
          changeset "**/worker/**"
        }
        agent{
          docker{
            image 'maven:3.6.1-jdk-8-slim'
            args '-v $HOME/.m2:/root/.m2'
          }
        }
        steps{
          echo 'Running Unit Tets on worker app..'
          dir('worker'){
            sh 'mvn clean test'
           }

          }
      }
      stage("worker-package"){
        when{
          branch 'master'
          changeset "**/worker/**"
        }
        agent{
          docker{
            image 'maven:3.6.1-jdk-8-slim'
            args '-v $HOME/.m2:/root/.m2'
          }
        }
        steps{
          echo 'Packaging worker app'
          dir('worker'){
            sh 'mvn package -DskipTests'
            archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
          }

        }
      }

      stage('worker-docker-package'){
          agent any
          when{
            changeset "**/worker/**"
            branch 'master'
          }
          steps{
            echo 'Packaging worker app with docker'
            script{
              docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                  def workerImage = docker.build("rahulselvam/worker:v${env.BUILD_ID}", "./worker")
                  workerImage.push()
                  workerImage.push("${env.BRANCH_NAME}")
                  workerImage.push("latest")
              }
            }
          }
      }
	  
	  stage("result-build")
{
when
{
changeset "**/result/**"
}
agent{
          docker{
            image 'node:8.16.0-alpine'
          }
        }
steps
{
echo 'Build result app'
dir('result')
{
sh 'npm install'
}
}
}
stage("result-test")
{
when
{
changeset "**/result/**"
}
agent{
          docker{
            image 'node:8.16.0-alpine'
          }
        }
steps
{
echo 'Running Tests on result app'
dir('result')
{
sh 'npm install && npm test'
}
}
}
stage('result-docker-package'){
          agent any
          when{
            changeset "**/result/**"
            branch 'master'
          }
          steps{
            echo 'Packaging result app with docker'
            script{
              docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                  def workerImage = docker.build("rahulselvam/result:v${env.BUILD_ID}", "./result")
                  workerImage.push()
                  workerImage.push("${env.BRANCH_NAME}")
                  workerImage.push("latest")
              }
            }
          }
      }
	  
	  stage("vote-build")
{
agent{
          docker{
            image 'python:2.7.16-slim'
            args '--user root'
          }
        }
when
{
changeset "**/vote/**"
}
steps
{
echo 'Build vote app'
dir('vote')
{
sh 'pip install -r requirements.txt'
}
}
}

stage("vote-test")
{
agent{
          docker{
            image 'python:2.7.16-slim'
            args '--user root'
          }
        }
when
{
changeset "**/vote/**"
}
steps
{
echo 'Running Tests on vote app'
dir('vote')
{
sh 'pip install -r requirements.txt'
sh 'nosetests -v'
}
}
}

stage('vote-docker-package'){
          agent any
          when{
            changeset "**/vote/**"
            branch 'master'
          }
          steps{
            echo 'Packaging vote app with docker'
            script{
              docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                  def workerImage = docker.build("rahulselvam/vote:v${env.BUILD_ID}", "./vote")
                  workerImage.push()
                  workerImage.push("${env.BRANCH_NAME}")
                  workerImage.push("latest")
              }
            }
          }
      }
	  
	  stage('deploy to dev')
	  {
		agent any
		when
		{
			branch 'master'
			}
		steps
		{
			echo 'Deploy instavote app with docker compose'
			sh 'docker-compose up -d'
		}
	}
  }

  post{
    always{
        echo 'Building multibranch pipeline for worker is completed..'
    }
  }
}
