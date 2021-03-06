# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
# KIND, either express or implied.  See the License for the
# specific language governing permissions and limitations
# under the License.

set(INTEGRATION_TESTS_SRCS
  cluster_itest_util.cc
  cluster_verifier.cc
  external_mini_cluster.cc
  external_mini_cluster_fs_inspector.cc
  log_verifier.cc
  mini_cluster.cc
  test_workload.cc
)

add_library(integration-tests ${INTEGRATION_TESTS_SRCS})
target_link_libraries(integration-tests
  tserver
  tserver_test_util
  master
  ksck
  kudu_client
  kudu_client_test_util
  kudu_fs
  kudu_test_util
  security-test)
add_dependencies(integration-tests
  kudu-tserver
  kudu-master
  # ExternalMiniCluster uses this shared library as a LD_PRELOAD
  krb5_realm_override)

# Tests
set(KUDU_TEST_LINK_LIBS integration-tests ${KUDU_MIN_TEST_LIBS})
ADD_KUDU_TEST(all_types-itest RESOURCE_LOCK "master-rpc-ports")
ADD_KUDU_TEST(alter_table-randomized-test)
ADD_KUDU_TEST(alter_table-test)
ADD_KUDU_TEST(client_failover-itest)
ADD_KUDU_TEST(client-stress-test
  RESOURCE_LOCK "master-rpc-ports"
  RUN_SERIAL true)
ADD_KUDU_TEST(create-table-itest)
ADD_KUDU_TEST(create-table-stress-test)
ADD_KUDU_TEST(delete_table-test)
ADD_KUDU_TEST(disk_reservation-itest)
ADD_KUDU_TEST(exactly_once_writes-itest)
ADD_KUDU_TEST(external_mini_cluster-test RESOURCE_LOCK "master-rpc-ports")
ADD_KUDU_TEST(fuzz-itest)
ADD_KUDU_TEST(linked_list-test RESOURCE_LOCK "master-rpc-ports")
ADD_KUDU_TEST(master_failover-itest RESOURCE_LOCK "master-rpc-ports")
ADD_KUDU_TEST(master_migration-itest RESOURCE_LOCK "master-rpc-ports")
ADD_KUDU_TEST_DEPENDENCIES(master_migration-itest
  kudu)
ADD_KUDU_TEST(master_replication-itest RESOURCE_LOCK "master-rpc-ports")
ADD_KUDU_TEST(master-stress-test RESOURCE_LOCK "master-rpc-ports")
ADD_KUDU_TEST(open-readonly-fs-itest)
ADD_KUDU_TEST(raft_consensus-itest RUN_SERIAL true)
ADD_KUDU_TEST(registration-test RESOURCE_LOCK "master-web-port")
ADD_KUDU_TEST(table_locations-itest)
ADD_KUDU_TEST(tablet_copy-itest)
ADD_KUDU_TEST(tablet_history_gc-itest)
ADD_KUDU_TEST(tablet_replacement-itest)
ADD_KUDU_TEST(ts_recovery-itest)
ADD_KUDU_TEST(ts_tablet_manager-itest)
ADD_KUDU_TEST(webserver-stress-itest)
ADD_KUDU_TEST(write_throttling-itest)

# Tests that should not be run automatically by ctest.
ADD_KUDU_TEST_NO_CTEST(version_migration-test)

# Some tests have additional dependencies
set(KUDU_TEST_LINK_LIBS kudu_tools_util ${KUDU_TEST_LINK_LIBS})
ADD_KUDU_TEST(flex_partitioning-itest)
ADD_KUDU_TEST(full_stack-insert-scan-test RUN_SERIAL true)
ADD_KUDU_TEST(update_scan_delta_compact-test RUN_SERIAL true)
