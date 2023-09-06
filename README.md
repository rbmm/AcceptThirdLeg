# AcceptThirdLeg
InitializeSecurityContext / AcceptSecurityContext
``````````````````````````````````````````````````````````````````
client app call CertServerSubmitRequest api. internally AcquireCredentialsHandle/InitializeSecurityContextW called for Negotiate
-----------------------------------------------------------------------------------------------------------------------------------
ntdll.dll!ZwAlpcSendWaitReceivePort + a
rpcrt4.dll!virtual long LRPC_CCALL::SendReceive(_RPC_MESSAGE *) + 169
rpcrt4.dll!NdrClientCall3 + e7d
rpcrt4.dll!NdrClientCall3 + fe
sspicli.dll!SspipProcessSecurityContext + 46c
sspicli.dll!long LsaInitializeSecurityContextCommon(_SecHandle *,_SecHandle *,unsigned short *,unsigned long,unsigned long,unsigned long,_SecBufferDesc *,unsigned long,_SecHandle *,_SecBufferDesc *,unsigned long *,_LARGE_INTEGER *,unsigned char) + 1da
sspicli.dll!long LsaInitializeSecurityContextW(_SecHandle *,_SecHandle *,unsigned short *,unsigned long,unsigned long,unsigned long,_SecBufferDesc *,unsigned long,_SecHandle *,_SecBufferDesc *,unsigned long *,_LARGE_INTEGER *) + 5a
sspicli.dll!SspipDeleteSecurityContext + 227
sspicli.dll!InitializeSecurityContextW + 70
rpcrt4.dll!long SECURITY_CONTEXT::InitializeFirstTime(SECURITY_CREDENTIALS *,unsigned short *,unsigned long,_SecBufferDesc *,int) + 1a2
rpcrt4.dll!int IsUserModeSocket(unsigned __int64,long *) + 550
rpcrt4.dll!long OSF_CCONNECTION::ActuallyDoBinding(OSF_CCALL *,int,unsigned long,OSF_BINDING * *,int *,FAILURE_COUNT_STATE *) + dd
rpcrt4.dll!long OSF_SCALL::SendRequestOrResponse(_RPC_MESSAGE *,unsigned char) + 3c6
rpcrt4.dll!long OSF_BINDING_HANDLE::InitCCallWithAssociation(_RPC_MESSAGE *,CLIENT_AUTH_INFO *,int,OSF_CCALL * *,int) + 8a
rpcrt4.dll!long OSF_BINDING_HANDLE::AllocateCCall(OSF_CCALL * *,_RPC_MESSAGE *,int *) + 233
rpcrt4.dll!virtual long OSF_BINDING_HANDLE::NegotiateTransferSyntax(_RPC_MESSAGE *) + 35
rpcrt4.dll!NdrClientCall3 + f6e
rpcrt4.dll!NdrClientCall3 + fe
certcli.dll!long crCertServerRequest(void *,int *,unsigned long,unsigned short const *,unsigned long *,unsigned long *,_CERTTRANSBLOB const *,_CERTTRANSBLOB const *,_CERTTRANSBLOB const *,_CERTTRANSBLOB *,_CERTTRANSBLOB *,_CERTTRANSBLOB *) + 204
certcli.dll!long crRequestCertificate(unsigned long,unsigned char const *,unsigned long,unsigned long,unsigned short const *,unsigned short const *,unsigned short const *,unsigned short const *,_CERTSERVERENROLL * *) + 1aa
certcli.dll!CertServerSubmitRequest + 66
CRT-UT.exe! + 3cfe
CRT-UT.exe! + 40c2
kernel32.dll!BaseThreadInitThunk + 22
ntdll.dll!RtlUserThreadStart + 34
``````````````````````````````````````````````````````````````````
in lsass process on client comp - NegInitLsaModeContext is called, which call WLsaInitContext for multiple AP. in concrete stack - this is kerberos SpInitLsaModeContext
``````````````````````````````````````````````````````````````````
kerberos.dll!SpInitLsaModeContext
lsasrv.dll!WLsaInitContext + 491
lsasrv.dll!long NegBuildRequestToken(int,_SecBufferDesc *,_NEG_CREDS *,_UNICODE_STRING *,unsigned long,unsigned long,MechTypeList *,_NEG_CONTEXT * *,_SecBufferDesc *,_LARGE_INTEGER *) + 941
lsasrv.dll!long NegInitLsaModeContext(unsigned __int64,unsigned __int64,_UNICODE_STRING *,unsigned long,unsigned long,_SecBufferDesc *,unsigned __int64 *,_SecBufferDesc *,unsigned long *,_LARGE_INTEGER *,unsigned char *,_SecBuffer *) + 291
lsasrv.dll!long NegInitLsaModeContext(unsigned __int64,unsigned __int64,_UNICODE_STRING *,unsigned long,unsigned long,_SecBufferDesc *,unsigned __int64 *,_SecBufferDesc *,unsigned long *,_LARGE_INTEGER *,unsigned char *,_SecBuffer *) + 23e
lsasrv.dll!SspiExProcessSecurityContext + 3d2
sspisrv.dll!SspirProcessSecurityContext + 1ef
rpcrt4.dll!Invoke + 73
rpcrt4.dll!Ndr64pServerUnMarshal + 1261
rpcrt4.dll!NdrServerCallAll + 3c
rpcrt4.dll!DispatchToStubInCNoAvrf + 37
rpcrt4.dll!long RPC_INTERFACE::DispatchToStubWorker(_RPC_MESSAGE *,unsigned int,int,long *) + 190
rpcrt4.dll!void LRPC_SASSOCIATION::HandleRequest(_PORT_MESSAGE *,_PORT_MESSAGE *,void *,unsigned __int64,int,RPCP_ALPC_TOKEN_ATTR *,RPCP_ALPC_HANDLE_ATTR *) + 6ff
rpcrt4.dll!void LRPC_SASSOCIATION::HandleRequest(_PORT_MESSAGE *,_PORT_MESSAGE *,void *,unsigned __int64,int,RPCP_ALPC_TOKEN_ATTR *,RPCP_ALPC_HANDLE_ATTR *) + 3e6
rpcrt4.dll!void LRPC_SASSOCIATION::HandleRequest(_PORT_MESSAGE *,_PORT_MESSAGE *,void *,unsigned __int64,int,RPCP_ALPC_TOKEN_ATTR *,RPCP_ALPC_HANDLE_ATTR *) + 238
rpcrt4.dll!void LrpcIoComplete(_TP_CALLBACK_INSTANCE *,void *,_TP_ALPC *,void *) + 2bd
rpcrt4.dll!void LrpcIoComplete(_TP_CALLBACK_INSTANCE *,void *,_TP_ALPC *,void *) + be
ntdll.dll!TppAlpcpExecuteCallback + 210
ntdll.dll!TppWorkerThread + 888
kernel32.dll!BaseThreadInitThunk + 22
ntdll.dll!RtlUserThreadStart + 34

``````````````````````````````````````````````````````````````````
on server comp in server app, AcquireCredentialsHandle/LsaAcceptSecurityContext(Negotiate) is called 
``````````````````````````````````````````````````````````````````

ntdll.dll!ZwAlpcSendWaitReceivePort + 14
rpcrt4.dll!long LRPC_BASE_CCALL::DoSendReceive(void) + 111
rpcrt4.dll!NdrpClientCall3 + a0e
rpcrt4.dll!NdrClientCall3 + f2
sspicli.dll!SspipProcessSecurityContext + 7b1
sspicli.dll!long LsaAcceptSecurityContext(_SecHandle *,_SecHandle *,_SecBufferDesc *,unsigned long,unsigned long,_SecHandle *,_SecBufferDesc *,unsigned long *,_LARGE_INTEGER *) + 1dd
sspicli.dll!AcceptSecurityContext + 24a
rpcrt4.dll!long SECURITY_CONTEXT::AcceptFirstTime(SECURITY_CREDENTIALS *,_SecBufferDesc *,_SecBufferDesc *,unsigned long,unsigned long,unsigned long) + 17f
rpcrt4.dll!long OSF_SCONNECTION::AcceptFirstTime(SECURITY_CREDENTIALS *,_SecBufferDesc *,_SecBufferDesc *,unsigned long,unsigned long,unsigned long) + 8b
rpcrt4.dll!int OSF_SCONNECTION::AlterContextRequested(rpcconn_bind *,unsigned int) + 6bb
rpcrt4.dll!void OSF_SCONNECTION::ProcessReceiveComplete(long,unsigned char *,unsigned int) + 6e9
rpcrt4.dll!void CO_ConnectionThreadPoolCallback(_TP_CALLBACK_INSTANCE *,void *,void *,unsigned long,unsigned __int64,_TP_IO *) + 16b
KernelBase.dll!BasepTpIoCallback + 50
ntdll.dll!TppIopExecuteCallback + 118
ntdll.dll!TppWorkerThread + 8ed
kernel32.dll!BaseThreadInitThunk + 14
ntdll.dll!RtlUserThreadStart + 21
``````````````````````````````````````````````````````````````````
on server comp in lsass NegAcceptLsaModeContext -> WLsaAcceptContext (can be multiple packages) -> SpAcceptLsaModeContext (in AP ) is called
this is final call to AcceptContext - kerberos create token for client - KerbCreateTokenFromTicketEx
``````````````````````````````````````````````````````````````````

kerberos.dll!long KerbCreateTokenFromTicketEx(int,int,_LUID *,KERB_AP_REQUEST *,KERB_ENCRYPTED_TICKET *,KERB_AUTHENTICATOR *,unsigned long,_KERB_ENCRYPTION_KEY *,_UNICODE_STRING *,_KERB_ENCRYPTION_KEY *,_LUID *,void * *,void * *,_UNICODE_STRING *,_UNICODE
kerberos.dll!SpAcceptLsaModeContext + 1333
lsasrv.dll!WLsaAcceptContext + 458
lsasrv.dll!long NegHandleClientRequest(unsigned __int64,_NEG_CONTEXT *,unsigned long,unsigned long,_SecBufferDesc *,unsigned __int64 *,_SecBufferDesc *,unsigned long *,_LARGE_INTEGER *,unsigned char *,_SecBuffer *) + 631
lsasrv.dll!long NegAcceptLsaModeContext(unsigned __int64,unsigned __int64,_SecBufferDesc *,unsigned long,unsigned long,unsigned __int64 *,_SecBufferDesc *,unsigned long *,_LARGE_INTEGER *,unsigned char *,_SecBuffer *) + 32d
lsasrv.dll!WLsaAcceptContext + 4ae
lsasrv.dll!SspiExProcessSecurityContext + 3b3
sspisrv.dll!SspirProcessSecurityContext + 20f
rpcrt4.dll!Invoke + 73
rpcrt4.dll!long Ndr64StubWorker(void *,void *,_RPC_MESSAGE *,_MIDL_SERVER_INFO_ *,long (*const *)(void),_MIDL_SYNTAX_INFO *,unsigned long *) + bfd
rpcrt4.dll!NdrServerCallAll + 3c
rpcrt4.dll!DispatchToStubInCNoAvrf + 24
rpcrt4.dll!long RPC_INTERFACE::DispatchToStubWorker(_RPC_MESSAGE *,unsigned int,int,long *) + 1bd
rpcrt4.dll!long RPC_INTERFACE::DispatchToStub(_RPC_MESSAGE *,unsigned int,int,long *,RPCP_INTERFACE_GROUP *) + cb
rpcrt4.dll!long LRPC_SCALL::DispatchRequest(int *) + 34c
rpcrt4.dll!void LRPC_SCALL::HandleRequest(_PORT_MESSAGE *,_PORT_MESSAGE *,void *,unsigned __int64,RPCP_ALPC_HANDLE_ATTR *) + 2bc
rpcrt4.dll!void LRPC_ADDRESS::HandleRequest(_PORT_MESSAGE *,RPCP_ALPC_MESSAGE_ATTRIBUTES *,_PORT_MESSAGE *,int) + 36c
rpcrt4.dll!void LRPC_ADDRESS::ProcessIO(void *) + 91b
rpcrt4.dll!void LrpcIoComplete(_TP_CALLBACK_INSTANCE *,void *,_TP_ALPC *,void *) + aa
ntdll.dll!TppAlpcpExecuteCallback + 25e
ntdll.dll!TppWorkerThread + 8d9
kernel32.dll!BaseThreadInitThunk + 14
ntdll.dll!RtlUserThreadStart + 21

``````````````````````````````````````````````````````````````````
client app is waiting...
``````````````````````````````````````````````````````````````````
ntdll.dll!ZwWaitForSingleObject + a
KernelBase.dll!WaitForSingleObjectEx + 98
rpcrt4.dll!unsigned long UTIL_WaitForSyncIO(_OVERLAPPED *,int,unsigned long) + 2e
rpcrt4.dll!long UTIL_GetOverlappedResultEx(void *,_OVERLAPPED *,unsigned long *,int,unsigned long) + 3b
rpcrt4.dll!long WS_SyncRecv(void *,unsigned char * *,unsigned int *,unsigned long) + 88
rpcrt4.dll!long CO_SubmitSyncRead(BASE_CONNECTION *,unsigned char * *,unsigned int *) + 116
rpcrt4.dll!long CO_SyncRecv(void *,unsigned char * *,unsigned int *,unsigned long) + 31c
rpcrt4.dll!long OSF_CCONNECTION::ActuallyDoBinding(OSF_CCALL *,int,unsigned long,OSF_BINDING * *,int *,FAILURE_COUNT_STATE *) + dd
rpcrt4.dll!long OSF_SCALL::SendRequestOrResponse(_RPC_MESSAGE *,unsigned char) + 3c6
rpcrt4.dll!long OSF_BINDING_HANDLE::InitCCallWithAssociation(_RPC_MESSAGE *,CLIENT_AUTH_INFO *,int,OSF_CCALL * *,int) + 8a
rpcrt4.dll!long OSF_BINDING_HANDLE::AllocateCCall(OSF_CCALL * *,_RPC_MESSAGE *,int *) + 233
rpcrt4.dll!virtual long OSF_BINDING_HANDLE::NegotiateTransferSyntax(_RPC_MESSAGE *) + 35
rpcrt4.dll!NdrClientCall3 + f6e
rpcrt4.dll!NdrClientCall3 + fe
certcli.dll!long crCertServerRequest(void *,int *,unsigned long,unsigned short const *,unsigned long *,unsigned long *,_CERTTRANSBLOB const *,_CERTTRANSBLOB const *,_CERTTRANSBLOB const *,_CERTTRANSBLOB *,_CERTTRANSBLOB *,_CERTTRANSBLOB *) + 204
certcli.dll!long crRequestCertificate(unsigned long,unsigned char const *,unsigned long,unsigned long,unsigned short const *,unsigned short const *,unsigned short const *,unsigned short const *,_CERTSERVERENROLL * *) + 1aa
certcli.dll!CertServerSubmitRequest + 66
CRT-UT.exe! + 3cfe
CRT-UT.exe! + 40c2
kernel32.dll!BaseThreadInitThunk + 22
ntdll.dll!RtlUserThreadStart + 34

``````````````````````````````````````````````````````````````````
server app begin AcceptThirdLeg
``````````````````````````````````````````````````````````````````
rpcrt4.dll!long SECURITY_CONTEXT::AcceptThirdLeg(unsigned long,_SecBufferDesc *,_SecBufferDesc *) + dc
rpcrt4.dll!long OSF_SCONNECTION::AcceptThirdLeg(unsigned long,_SecBufferDesc *,_SecBufferDesc *) + 62
rpcrt4.dll!int OSF_SCONNECTION::AlterContextRequested(rpcconn_bind *,unsigned int) + 41d
rpcrt4.dll!void OSF_SCONNECTION::ProcessReceiveComplete(long,unsigned char *,unsigned int) + 6e9
rpcrt4.dll!void CO_ConnectionThreadPoolCallback(_TP_CALLBACK_INSTANCE *,void *,void *,unsigned long,unsigned __int64,_TP_IO *) + 16b
KernelBase.dll!BasepTpIoCallback + 50
ntdll.dll!TppIopExecuteCallback + 118
ntdll.dll!TppWorkerThread + 8ed
kernel32.dll!BaseThreadInitThunk + 14
ntdll.dll!RtlUserThreadStart + 21

``````````````````````````````````````````````````````````````````
server app EatAuthInfoFromPacket
``````````````````````````````````````````````````````````````````
rpcrt4.dll!long OSF_SCALL::EatAuthInfoFromPacket(rpcconn_request *,unsigned long *,int)
rpcrt4.dll!int OSF_SCALL::ProcessReceivedPDU(rpcconn_common *,unsigned int,int) + 6a
rpcrt4.dll!void OSF_SCONNECTION::ProcessReceiveComplete(long,unsigned char *,unsigned int) + 410
rpcrt4.dll!void CO_ConnectionThreadPoolCallback(_TP_CALLBACK_INSTANCE *,void *,void *,unsigned long,unsigned __int64,_TP_IO *) + 16b
KernelBase.dll!BasepTpIoCallback + 50
ntdll.dll!TppIopExecuteCallback + 118
ntdll.dll!TppWorkerThread + 8ed
kernel32.dll!BaseThreadInitThunk + 14
ntdll.dll!RtlUserThreadStart + 21
``````````````````````````````````````````````````````````````````
finally target function ( s_CertServerRequest) is called. server do CheckCertSrvAccess - open client token created inside KerbCreateTokenFromTicketEx
``````````````````````````````````````````````````````````````````
KernelBase.dll!OpenThreadToken + 1c
certsrv.exe!long CheckCertSrvAccess(unsigned short const *,void *,unsigned long,int *,void * *) + 18a
certsrv.exe!s_CertServerRequest + 293
rpcrt4.dll!Invoke + 73
rpcrt4.dll!long Ndr64StubWorker(void *,void *,_RPC_MESSAGE *,_MIDL_SERVER_INFO_ *,long (*const *)(void),_MIDL_SYNTAX_INFO *,unsigned long *) + bfd
rpcrt4.dll!NdrServerCallAll + 3c
rpcrt4.dll!DispatchToStubInCNoAvrf + 24
rpcrt4.dll!long RPC_INTERFACE::DispatchToStubWorker(_RPC_MESSAGE *,unsigned int,int,long *) + 1bd
rpcrt4.dll!void OSF_SCALL::DispatchHelper(void) + 1b8
rpcrt4.dll!int OSF_SCALL::ProcessReceivedPDU(rpcconn_common *,unsigned int,int) + 1dd
rpcrt4.dll!void OSF_SCONNECTION::ProcessReceiveComplete(long,unsigned char *,unsigned int) + 410
rpcrt4.dll!void CO_ConnectionThreadPoolCallback(_TP_CALLBACK_INSTANCE *,void *,void *,unsigned long,unsigned __int64,_TP_IO *) + 16b
KernelBase.dll!BasepTpIoCallback + 50
ntdll.dll!TppIopExecuteCallback + 118
ntdll.dll!TppWorkerThread + 8ed
kernel32.dll!BaseThreadInitThunk + 14
ntdll.dll!RtlUserThreadStart + 21

``````````````````````````````````````````````````````````````````
and check access
``````````````````````````````````````````````````````````````````
advapi32.dll!AccessCheckStub
certsrv.exe!long CheckCertSrvAccess(unsigned short const *,void *,unsigned long,int *,void * *) + 20b
certsrv.exe!s_CertServerRequest + 293
rpcrt4.dll!Invoke + 73
rpcrt4.dll!long Ndr64StubWorker(void *,void *,_RPC_MESSAGE *,_MIDL_SERVER_INFO_ *,long (*const *)(void),_MIDL_SYNTAX_INFO *,unsigned long *) + bfd
rpcrt4.dll!NdrServerCallAll + 3c
rpcrt4.dll!DispatchToStubInCNoAvrf + 24
rpcrt4.dll!long RPC_INTERFACE::DispatchToStubWorker(_RPC_MESSAGE *,unsigned int,int,long *) + 1bd
rpcrt4.dll!void OSF_SCALL::DispatchHelper(void) + 1b8
rpcrt4.dll!int OSF_SCALL::ProcessReceivedPDU(rpcconn_common *,unsigned int,int) + 1dd
rpcrt4.dll!void OSF_SCONNECTION::ProcessReceiveComplete(long,unsigned char *,unsigned int) + 410
rpcrt4.dll!void CO_ConnectionThreadPoolCallback(_TP_CALLBACK_INSTANCE *,void *,void *,unsigned long,unsigned __int64,_TP_IO *) + 16b
KernelBase.dll!BasepTpIoCallback + 50
ntdll.dll!TppIopExecuteCallback + 118
ntdll.dll!TppWorkerThread + 8ed
kernel32.dll!BaseThreadInitThunk + 14
ntdll.dll!RtlUserThreadStart + 21

``````````````````````````````````````````````````````````````````
get client name GetClientUserName
``````````````````````````````````````````````````````````````````
certsrv.exe!long GetClientUserName(void *,unsigned short * *,unsigned short * *)
certsrv.exe!s_CertServerRequest + 432
rpcrt4.dll!Invoke + 73
rpcrt4.dll!long Ndr64StubWorker(void *,void *,_RPC_MESSAGE *,_MIDL_SERVER_INFO_ *,long (*const *)(void),_MIDL_SYNTAX_INFO *,unsigned long *) + bfd
rpcrt4.dll!NdrServerCallAll + 3c
rpcrt4.dll!DispatchToStubInCNoAvrf + 24
rpcrt4.dll!long RPC_INTERFACE::DispatchToStubWorker(_RPC_MESSAGE *,unsigned int,int,long *) + 1bd
rpcrt4.dll!void OSF_SCALL::DispatchHelper(void) + 1b8
rpcrt4.dll!int OSF_SCALL::ProcessReceivedPDU(rpcconn_common *,unsigned int,int) + 1dd
rpcrt4.dll!void OSF_SCONNECTION::ProcessReceiveComplete(long,unsigned char *,unsigned int) + 410
rpcrt4.dll!void CO_ConnectionThreadPoolCallback(_TP_CALLBACK_INSTANCE *,void *,void *,unsigned long,unsigned __int64,_TP_IO *) + 16b
KernelBase.dll!BasepTpIoCallback + 50
ntdll.dll!TppIopExecuteCallback + 118
ntdll.dll!TppWorkerThread + 8ed
kernel32.dll!BaseThreadInitThunk + 14
ntdll.dll!RtlUserThreadStart + 21

``````````````````````````````````````````````````````````````````
only now execute CoreProcessRequest
``````````````````````````````````````````````````````````````````
long CoreProcessRequest(unsigned long,unsigned short const *,unsigned long,unsigned char const *,unsigned short const *,unsigned short const *,unsigned long,unsigned long,_CERTSRV_RESULT_CONTEXT *)
``````````````````````````````````````````````````````````````````


another example with schannel here - https://github.com/rbmm/LDAPS#readme

