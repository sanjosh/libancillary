libancillary
============

Pass file descriptors between processes using Unix domain sockets

Clone of http://www.normalesup.org/~george/comp/libancillary/


Overview

This library provide an easy interface to the black magic that can be done on Unix domain sockets, like passing file descriptors from one process to another. It is supposed to be 100% conform to the Single Unix Specification v3 and has been tested under Linux (2.4), Solaris (8), OpenBSD (2.7) and FreeBSD (4.2). The license is a BSD style one.
Download

The latest version of libancillary is available on Gitorious. You can clone the repository using the following command:

git clone git://gitorious.org/libancillary/libancillary.git

    Tarball from Gitorious (slow; use Git instead)
    libancillary-0.9.1.tar.gz (4.6 ko)

Documentation

  This library provide an easy interface to the black magic that can be done
  on Unix domain sockets, like passing file descriptors from one process to
  another.

  Programs that uses this library should include the ancillary.h header file.
  Nothing else is required.

  All functions of this library require the following header:

	#include <ancillary.h>

  At this time, the only ancillary data defined by the Single Unix
  Specification (v3) is file descriptors.

Passing file descriptors

	int ancil_send_fd(socket, file_descriptor)
	  int socket: the Unix socket
	  int file_descriptor: the file descriptor
	  Return value: 0 for success, -1 for failure.

  Sends one file descriptor on a socket.
  In case of failure, errno is set; the possible values are the ones of the
  sendmsg(2) system call.


	int ancil_recv_fd(socket, file_descriptor)
	  int socket: the Unix socket
	  int *file_descriptor: pointer to the returned file descriptor
	  Return value: 0 for success, -1 for failure

  Receives one file descriptor from a socket.
  In case of success, the file descriptor is stored in the integer pointed
  to by file_descriptor.
  In case of failure, errno is set; the possible values are the ones of the
  recvmsg(2) system call.
  The behavior is undefined if the recv_fd does not match a send_fd* on the
  other side.


	int ancil_send_fds(socket, file_descriptors, num_file_descriptors)
	  int socket: the Unix socket
	  const int *file_descriptors: array of file descriptors
	  unsigned num_file_descriptors: number of file descriptors
	  Return value: 0 for success, -1 for failure

  Sends several file descriptors on a socket.
  In case of failure, errno is set; the possible values are the ones of the
  sendmsg(2) system call.
  The maximum number of file descriptors that can be sent using this
  function is ANCIL_MAX_N_FDS; the behavior is undefined in case of
  overflow, probably a stack corruption.


	int ancil_recv_fds(socket, file_descriptors, num_file_descriptors)
	  int socket: the Unix socket
	  int *file_descriptors: return array of file descriptors
	  unsigned num_file_descriptors: number of file descriptors
	  Return value: number of received fd for success, -1 for failure

  Receives several file descriptors from a socket, no more than
  num_file_descriptors.
  In case of success, the received file descriptors are stored in the array
  pointed to by file_descriptors.
  In case of failure, errno is set; the possible values are the ones of the
  recvmsg(2) system call.
  The maximum number of file descriptors that can be received using this
  function is ANCIL_MAX_N_FDS; the behavior is undefined in case of
  overflow, probably a stack corruption.
  The behavior is undefined if the recv_fds does not match a send_fd* on
  the other side, or if the number of received file descriptors is more than
  num_file_descriptors.


	int ancil_send_fds_with_buffer(socket, fds, num, buffer)
	  int socket: the Unix socket
	  const int *fds: array of file descriptors
	  unsigned num: number of file descriptors
	  void *buffer: buffer to hold the system data structures
	  Return value: 0 for success, -1 for failure

  Sends several file descriptors on a socket.
  In case of failure, errno is set; the possible values are the ones of the
  sendmsg(2) system call.
  The buffer argument must point to a memory area large enough to hold the
  system data structures, see ANCIL_FD_BUFFER.


	int ancil_recv_fds_with_buffer(socket, fds, num, buffer)
	  int socket: the Unix socket
	  int *fds: return array of file descriptors
	  unsigned num: number of file descriptors
	  void *buffer: buffer to hold the system data structures
	  Return value: number of received fd for success, -1 for failure

  Receives several file descriptors from a socket, no more than
  num_file_descriptors.
  In case of success, the received file descriptors are stored in the array
  pointed to by file_descriptors.
  In case of failure, errno is set; the possible values are the ones of the
  recvmsg(2) system call.
  The behavior is undefined if the recv_fds does not match a send_fd* on
  the other side, or if the number of received file descriptors is more than
  num_file_descriptors.
  The buffer argument must point to a memory area large enough to hold the
  system data structures, see ANCIL_FD_BUFFER.


	ANCIL_MAX_N_FDS

  Maximum number of file descriptors that can be sent with the sent_fds and
  recv_fds functions. If you have to send more at once, use the
  *_with_buffer versions. The value is enough to send "quite a few" file
  descriptors.


	ANCIL_FD_BUFFER(n)
	  int n: number of file descriptors

  Expands to a structure data type large enough to hold the system data
  structures for n file descriptors. So the address of a variable declared
  of type ANCIL_FD_BUFFER(n) is suitable as the buffer argument for
  *_with_buffer on n file descriptors.
  To use this macro, you need <sys/types.h> and <sys/socket.h>. Bevare: with
  Solaris, the _XPG4_2 macro must be defined before sys/socket is included.


Tuning the compilation

  This library is designed to be included in projects, not installed in
  /usr/lib. If your project does not use some of the functions, the
  TUNE_OPTS variable in the Makefile allows not to build them. It is a list
  of proprocessor options:

  -DNDEBUG: turn assertions off (see assert(3))
  -DSPARE_SEND_FDS: do not build ancil_send_fds
  -DSPARE_SEND_FD:  do not build ancil_send_fd
  -DSPARE_RECV_FDS: do not build ancil_recv_fds
  -DSPARE_RECV_FD:  do not build ancil_recv_fd

Copying

libancillary - black magic on Unix domain sockets
(C) Nicolas George

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

 1. Redistributions of source code must retain the above copyright notice,
    this list of conditions and the following disclaimer.
 2. Redistributions in binary form must reproduce the above copyright
    notice, this list of conditions and the following disclaimer in the
    documentation and/or other materials provided with the distribution.
 3. The name of the author may not be used to endorse or promote products
    derived from this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE AUTHOR ``AS IS'' AND ANY EXPRESS OR IMPLIED
WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF
MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO
EVENT SHALL THE AUTHOR BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO,
PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS;
OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY,
WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR
OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF
ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.


