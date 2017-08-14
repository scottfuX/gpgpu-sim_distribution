pipeline {
    agent {
        label "purdue-cluster"
        }

    stages {
        stage('4.2-simulator-build')
        parallel 4.2: {
            steps {
                sh 'source /home/tgrogers-raid/a/common/gpgpu-sim-setup/4.2_env_setup.sh &&\
                source `pwd`/setup_environment && \
                make -j'
            }
        }, 8.0: {
            steps {
                sh 'source /home/tgrogers-raid/a/common/gpgpu-sim-setup/8.0_env_setup.sh &&\
                source `pwd`/setup_environment && \
                make -j'
            }
        }
        stage('4.2-simulations-build'){
            steps{
                sh 'source /home/tgrogers-raid/a/common/gpgpu-sim-setup/4.2_env_setup.sh &&\
                source `pwd`/setup_environment && \
                rm -rf gpgpu-sim_simulations && \
                git clone git@github.rcac.purdue.edu:TimRogersGroup/gpgpu-sim_simulations.git && \
                cd gpgpu-sim_simulations && \
                git checkout purdue-cluster && \
                source ./benchmarks/src/setup_environment && \
                make -j -C ./benchmarks/src all'
            }
        }
        stage('4.2-rodinia-regress'){
            steps {
                sh 'source /home/tgrogers-raid/a/common/gpgpu-sim-setup/4.2_env_setup.sh &&\
                source `pwd`/setup_environment && \
                ./gpgpu-sim_simulations/util/job_launching/run_simulations.py -N regress && \
                ./gpgpu-sim_simulations/util/job_launching/monitor_func_test.py -v -N regress'
            }
        }
        }
    post {
        always{
            emailext body: "See ${BUILD_URL}",
                recipientProviders: [[$class: 'CulpritsRecipientProvider'],
                    [$class: 'RequesterRecipientProvider']],
                subject: "[AALP Jenkins] Build #${BUILD_NUMBER} - ${currentBuild.result}",
                to: 'tgrogers@purdue.edu'

        }
    }
}
